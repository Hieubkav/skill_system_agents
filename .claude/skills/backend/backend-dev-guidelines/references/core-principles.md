# Core Principles

7 nguyên tắc quan trọng nhất cho backend development với Langfuse stack.

---

## 1. tRPC Procedures Delegate to Services

**Principle**: tRPC procedures chỉ xử lý routing và validation. Business logic phải ở services.

### Why?

- **Separation of Concerns**: Procedures handle HTTP, services handle business logic
- **Reusability**: Services có thể dùng từ nhiều procedures hoặc queue processors
- **Testability**: Dễ test services độc lập với HTTP layer

### Examples

#### ❌ NEVER: Business logic in procedures

```typescript
export const traceRouter = createTRPCRouter({
  byId: protectedProjectProcedure
    .input(z.object({ traceId: z.string() }))
    .query(async ({ input, ctx }) => {
      // ❌ 200 lines of business logic here
      const trace = await ctx.prisma.trace.findUnique({
        where: { id: input.traceId, projectId: ctx.session.projectId },
        include: {
          observations: {
            include: { scores: true },
          },
        },
      });

      if (!trace) {
        throw new TRPCError({ code: "NOT_FOUND" });
      }

      // Complex calculations
      const totalCost = trace.observations.reduce(
        (sum, obs) => sum + (obs.calculatedTotalCost?.toNumber() || 0),
        0
      );

      // More logic...
      return { trace, totalCost };
    }),
});
```

#### ✅ ALWAYS: Delegate to service

```typescript
// tRPC Procedure
export const traceRouter = createTRPCRouter({
  byId: protectedProjectProcedure
    .input(z.object({ traceId: z.string() }))
    .query(async ({ input, ctx }) => {
      return await getTraceById(input.traceId, ctx.session.projectId);
    }),
});

// Service
export async function getTraceById(traceId: string, projectId: string) {
  const trace = await prisma.trace.findUnique({
    where: { id: traceId, projectId },
    include: {
      observations: { include: { scores: true } },
    },
  });

  if (!trace) {
    throw new TRPCError({ code: "NOT_FOUND", message: "Trace not found" });
  }

  const totalCost = trace.observations.reduce(
    (sum, obs) => sum + (obs.calculatedTotalCost?.toNumber() || 0),
    0
  );

  return { trace, totalCost };
}
```

---

## 2. Access Config via env.mjs, NEVER process.env

**Principle**: Tất cả environment variables phải access qua validated config objects.

### Why?

- **Type Safety**: env.mjs provides TypeScript types
- **Validation**: Zod validates all env vars at startup
- **Single Source of Truth**: One place to manage all config
- **Fail Fast**: App won't start với invalid config

### Examples

#### ❌ NEVER (except in env.mjs itself)

```typescript
const dbUrl = process.env.DATABASE_URL;
const port = parseInt(process.env.PORT || "3000");
const apiKey = process.env.OPENAI_API_KEY;

// Problems:
// - No type safety
// - No validation
// - Typos won't be caught
// - Runtime errors if missing
```

#### ✅ ALWAYS

```typescript
// Web package
import { env } from "@/src/env.mjs";

const dbUrl = env.DATABASE_URL; // Type-safe, validated
const cloudRegion = env.NEXT_PUBLIC_LANGFUSE_CLOUD_REGION;

// Worker package
import { env } from "./env";

const redisUrl = env.REDIS_CONNECTION_STRING;
const batchSize = env.LANGFUSE_WORKER_BATCH_SIZE;
```

### env.mjs Schema Example

```typescript
// env.mjs
import { createEnv } from "@t3-oss/env-nextjs";
import { z } from "zod/v4";

export const env = createEnv({
  server: {
    DATABASE_URL: z.string().url(),
    REDIS_CONNECTION_STRING: z.string().optional(),
    OPENAI_API_KEY: z.string().min(1),
  },
  client: {
    NEXT_PUBLIC_LANGFUSE_CLOUD_REGION: z
      .enum(["US", "EU", "STAGING", "DEV"])
      .optional(),
  },
  runtimeEnv: {
    DATABASE_URL: process.env.DATABASE_URL,
    REDIS_CONNECTION_STRING: process.env.REDIS_CONNECTION_STRING,
    OPENAI_API_KEY: process.env.OPENAI_API_KEY,
    NEXT_PUBLIC_LANGFUSE_CLOUD_REGION:
      process.env.NEXT_PUBLIC_LANGFUSE_CLOUD_REGION,
  },
});
```

---

## 3. Validate ALL Input with Zod v4

**Principle**: Mọi external input (API requests, queue jobs, external data) phải validate với Zod schemas.

### Why?

- **Type Safety**: Runtime validation + TypeScript types
- **Defense in Depth**: Catch invalid data early
- **Better Errors**: Clear validation error messages
- **Documentation**: Schemas document expected input shape

### Examples

#### ❌ NEVER: No validation

```typescript
export const datasetRouter = createTRPCRouter({
  create: protectedProjectProcedure.mutation(async ({ input, ctx }) => {
    // ❌ No validation - dangerous!
    const dataset = await prisma.dataset.create({
      data: {
        name: input.name, // Could be undefined, null, or malicious
        projectId: ctx.session.projectId,
      },
    });
    return dataset;
  }),
});
```

#### ✅ ALWAYS: Validate with Zod v4

```typescript
import { z } from "zod/v4";

const createDatasetSchema = z.object({
  name: z.string().min(1).max(100),
  description: z.string().optional(),
  metadata: z.record(z.unknown()).optional(),
});

export const datasetRouter = createTRPCRouter({
  create: protectedProjectProcedure
    .input(createDatasetSchema)
    .mutation(async ({ input, ctx }) => {
      // input is now type-safe and validated
      const dataset = await prisma.dataset.create({
        data: {
          name: input.name,
          description: input.description,
          metadata: input.metadata,
          projectId: ctx.session.projectId,
        },
      });
      return dataset;
    }),
});
```

### Zod v4 Tips

```typescript
// Custom error messages
z.string().min(1, "Name is required");

// Transformations
z.string().transform((val) => val.toLowerCase());

// Refinements
z.object({ start: z.date(), end: z.date() }).refine(
  (data) => data.end > data.start,
  "End date must be after start date"
);

// Reusable schemas
const paginationSchema = z.object({
  page: z.number().int().positive().default(1),
  limit: z.number().int().positive().max(100).default(50),
});
```

---

## 4. Services Use Prisma Directly for Simple CRUD or Repositories for Complex Queries

**Principle**: Simple CRUD operations dùng Prisma trực tiếp. Complex queries (traces, observations, scores) dùng repositories.

### Why?

- **Simplicity**: Don't over-engineer simple operations
- **Performance**: Repositories optimize complex queries
- **Reusability**: Share complex query logic across features
- **Maintainability**: One place to update query patterns

### Examples

#### Simple CRUD: Use Prisma Directly

```typescript
// ✅ Simple operations in services
import { prisma } from "@langfuse/shared/src/db";

export async function createDataset(data: CreateDatasetInput, projectId: string) {
  return await prisma.dataset.create({
    data: {
      name: data.name,
      description: data.description,
      projectId, // Always filter by projectId!
    },
  });
}

export async function getDatasetById(id: string, projectId: string) {
  return await prisma.dataset.findUnique({
    where: { id, projectId }, // Tenant isolation
    include: { items: true },
  });
}
```

#### Complex Queries: Use Repositories

```typescript
// ✅ Complex queries in repositories
import { getTracesTable } from "@langfuse/shared/src/server";

export async function getFilteredTraces(params: {
  projectId: string;
  filter: FilterState;
  limit: number;
  page: number;
}) {
  // Repository handles complex filtering, sorting, pagination
  const traces = await getTracesTable({
    projectId: params.projectId,
    filter: params.filter,
    searchQuery: undefined,
    limit: params.limit,
    page: params.page,
    orderBy: { column: "timestamp", order: "DESC" },
  });

  return traces;
}
```

### When to Use Which?

| Operation Type                | Use                  | Example                            |
| ----------------------------- | -------------------- | ---------------------------------- |
| Simple CRUD                   | Prisma directly      | Create/update/delete dataset       |
| Find by ID                    | Prisma directly      | `findUnique({ where: { id } })`    |
| Complex filtering             | Repository           | Traces with multiple filters       |
| Aggregations                  | Repository           | Score statistics                   |
| Cross-table queries           | Repository           | Observations with model data       |
| Performance-critical queries  | Repository           | Large dataset queries              |
| ClickHouse queries            | Repository           | Analytics queries                  |

---

## 5. Always Filter by projectId for Tenant Isolation

**Principle**: Mọi database query phải filter by projectId để ensure multi-tenant isolation.

### Why?

- **Security**: Prevent data leakage between projects
- **Compliance**: Required for multi-tenant architecture
- **Data Integrity**: Ensure queries only access authorized data

### Examples

#### ❌ NEVER: Missing projectId filter

```typescript
// ❌ SECURITY RISK - No tenant isolation!
const trace = await prisma.trace.findUnique({
  where: { id: traceId }, // Missing projectId!
});

// User could access traces from other projects!
```

#### ✅ ALWAYS: Include projectId filter

```typescript
// ✅ CORRECT: Tenant isolation
const trace = await prisma.trace.findUnique({
  where: { 
    id: traceId, 
    projectId, // Required for multi-tenant data isolation
  },
});

// ✅ For lists/searches
const traces = await prisma.trace.findMany({
  where: {
    projectId, // Always required
    name: { contains: searchQuery },
  },
});

// ✅ ClickHouse queries also require projectId
const traces = await queryClickhouse({
  query: `
    SELECT * FROM traces
    WHERE project_id = {projectId: String}
    AND timestamp >= {startTime: DateTime64(3)}
  `,
  params: { projectId, startTime },
});
```

---

## 6. Observability: OpenTelemetry + DataDog (Not Sentry for Backend)

**Principle**: Backend uses OpenTelemetry for observability, với traces và logs sent to DataDog.

### Why?

- **Distributed Tracing**: Track requests across services
- **Performance Monitoring**: Identify bottlenecks
- **Structured Logging**: Context-rich logs with trace IDs
- **Correlation**: Link logs to traces to spans

### Examples

#### Structured Logging

```typescript
import { logger } from "@langfuse/shared/src/server";

// ✅ Structured logging (includes trace_id, span_id, dd.trace_id)
logger.info("Processing dataset", { datasetId, projectId });
logger.error("Failed to create dataset", { 
  error: err.message, 
  datasetId,
  projectId 
});

// ❌ Don't use console.log
console.log("Processing dataset"); // No context, no trace correlation
```

#### Record Exceptions

```typescript
import { traceException } from "@langfuse/shared/src/server";

try {
  await operation();
} catch (error) {
  traceException(error); // Records to current OpenTelemetry span
  throw error; // Re-throw to propagate
}
```

#### Instrument Critical Operations

```typescript
import { instrumentAsync } from "@langfuse/shared/src/server";

const result = await instrumentAsync(
  { name: "dataset.create" },
  async (span) => {
    span.setAttributes({ datasetId, projectId });
    
    const dataset = await prisma.dataset.create({ data });
    
    span.setAttributes({ 
      "dataset.name": dataset.name,
      "dataset.items_count": 0,
    });
    
    return dataset;
  }
);
```

### Note

- **Frontend**: Uses Sentry
- **Backend** (tRPC, API routes, services, worker): Uses OpenTelemetry + DataDog

---

## 7. Comprehensive Testing Required

**Principle**: Tất cả features và bug fixes phải có tests.

### Why?

- **Confidence**: Deploy without fear
- **Documentation**: Tests document expected behavior
- **Refactoring**: Safe to refactor with test coverage
- **Regression Prevention**: Catch bugs before production

### Test Types

| Type        | Framework | Location                                | Purpose                      |
| ----------- | --------- | --------------------------------------- | ---------------------------- |
| Integration | Jest      | `web/src/__tests__/async/`              | Full API endpoint testing    |
| tRPC        | Jest      | `web/src/__tests__/async/`              | tRPC procedures with auth    |
| Service     | Jest      | `web/src/__tests__/async/repositories/` | Repository/service functions |
| Worker      | Vitest    | `worker/src/__tests__/`                 | Queue processors & streams   |

### Quick Examples

```typescript
// Integration Test (Public API)
const res = await makeZodVerifiedAPICall(
  PostDatasetsV1Response,
  "POST",
  "/api/public/datasets",
  { name: "test-dataset" },
  auth
);
expect(res.status).toBe(200);

// tRPC Test
const { caller } = await prepare(); // Creates session + caller
const response = await caller.automations.getAutomations({ projectId });
expect(response).toHaveLength(1);

// Service Test
const result = await getObservationsWithModelDataFromEventsTable({
  projectId,
  filter: [],
  limit: 1000,
  offset: 0,
});
expect(result.length).toBeGreaterThan(0);

// Worker Test (vitest)
const stream = await getObservationStream({ projectId, filter: [] });
const rows = [];
for await (const chunk of stream) rows.push(chunk);
expect(rows).toHaveLength(2);
```

### Key Principles

- Use unique IDs (`randomUUID()`) to avoid test interference
- Clean up test data or use unique project IDs
- Tests must be independent and runnable in any order
- Never use `pruneDatabase` in tests

See [testing-guide.md](./testing-guide.md) for complete details.

---

## Summary Checklist

Khi code, luôn hỏi:

- [ ] Business logic ở service, không phải procedure? ✅
- [ ] Dùng env.mjs thay vì process.env? ✅
- [ ] Input đã validate với Zod v4? ✅
- [ ] Complex queries dùng repositories? ✅
- [ ] Có filter by projectId cho tenant isolation? ✅
- [ ] Error logging với logger/traceException? ✅
- [ ] Có tests cho feature mới? ✅
