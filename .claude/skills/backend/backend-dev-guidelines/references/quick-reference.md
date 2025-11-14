# Quick Reference

Cheat sheet nhanh cho backend development.

---

## HTTP Status Codes

| Code | Use Case | Example |
|------|----------|---------|
| **200** | Success | GET request successful |
| **201** | Created | POST created new resource |
| **204** | No Content | DELETE successful, no body |
| **400** | Bad Request | Invalid input data |
| **401** | Unauthorized | Missing or invalid auth |
| **403** | Forbidden | Authenticated but no permission |
| **404** | Not Found | Resource doesn't exist |
| **409** | Conflict | Resource already exists |
| **422** | Unprocessable Entity | Validation failed |
| **429** | Too Many Requests | Rate limit exceeded |
| **500** | Server Error | Unexpected server error |
| **503** | Service Unavailable | Service temporarily down |

---

## tRPC Error Codes

```typescript
import { TRPCError } from "@trpc/server";

// Common error codes
throw new TRPCError({ code: "UNAUTHORIZED" });
throw new TRPCError({ code: "FORBIDDEN" });
throw new TRPCError({ code: "NOT_FOUND" });
throw new TRPCError({ code: "BAD_REQUEST" });
throw new TRPCError({ code: "INTERNAL_SERVER_ERROR" });

// With custom message
throw new TRPCError({
  code: "NOT_FOUND",
  message: "Dataset not found",
});

// With cause
throw new TRPCError({
  code: "INTERNAL_SERVER_ERROR",
  message: "Failed to create dataset",
  cause: originalError,
});
```

| tRPC Code | HTTP Status | Use When |
|-----------|-------------|----------|
| `UNAUTHORIZED` | 401 | No valid session/auth |
| `FORBIDDEN` | 403 | Authenticated but no access |
| `NOT_FOUND` | 404 | Resource doesn't exist |
| `BAD_REQUEST` | 400 | Invalid input |
| `CONFLICT` | 409 | Resource already exists |
| `INTERNAL_SERVER_ERROR` | 500 | Unexpected error |

---

## Prisma Quick Patterns

### CRUD Operations

```typescript
// Create
const user = await prisma.user.create({
  data: { email, name, projectId },
});

// Read
const user = await prisma.user.findUnique({
  where: { id, projectId }, // Always include projectId!
});

const users = await prisma.user.findMany({
  where: { projectId },
  orderBy: { createdAt: "desc" },
  take: 10,
});

// Update
const user = await prisma.user.update({
  where: { id, projectId },
  data: { name: "New Name" },
});

// Delete
await prisma.user.delete({
  where: { id, projectId },
});
```

### Relations

```typescript
// Include relations
const dataset = await prisma.dataset.findUnique({
  where: { id, projectId },
  include: {
    items: true,
    runs: { where: { status: "COMPLETED" } },
  },
});

// Create with nested
await prisma.dataset.create({
  data: {
    name: "Test Dataset",
    projectId,
    items: {
      create: [
        { input: { text: "Hello" }, expectedOutput: { response: "Hi" } },
      ],
    },
  },
});
```

### Pagination

```typescript
const page = 1;
const limit = 50;
const skip = (page - 1) * limit;

const [items, total] = await Promise.all([
  prisma.dataset.findMany({
    where: { projectId },
    skip,
    take: limit,
    orderBy: { createdAt: "desc" },
  }),
  prisma.dataset.count({ where: { projectId } }),
]);

return {
  data: items,
  meta: {
    page,
    limit,
    totalItems: total,
    totalPages: Math.ceil(total / limit),
  },
};
```

### Transactions

```typescript
await prisma.$transaction([
  prisma.dataset.create({ data: datasetData }),
  prisma.auditLog.create({ data: logData }),
]);

// Interactive transaction
await prisma.$transaction(async (tx) => {
  const dataset = await tx.dataset.create({ data: datasetData });
  await tx.datasetItem.createMany({
    data: items.map((item) => ({ ...item, datasetId: dataset.id })),
  });
  return dataset;
});
```

---

## Zod v4 Quick Patterns

```typescript
import { z } from "zod/v4";

// Basic types
z.string();
z.number();
z.boolean();
z.date();
z.null();
z.undefined();

// String validations
z.string().min(1, "Required");
z.string().max(100);
z.string().email();
z.string().url();
z.string().uuid();
z.string().regex(/^\d+$/);

// Number validations
z.number().int();
z.number().positive();
z.number().min(0).max(100);

// Objects
z.object({
  name: z.string(),
  age: z.number().optional(),
});

// Arrays
z.array(z.string());
z.string().array(); // Same as above

// Enums
z.enum(["admin", "user"]);
z.nativeEnum(Role); // Prisma enum

// Optional and nullable
z.string().optional(); // string | undefined
z.string().nullable(); // string | null
z.string().nullish(); // string | null | undefined

// Defaults
z.string().default("default value");
z.number().default(0);

// Transformations
z.string().transform((val) => val.toLowerCase());
z.string().transform((val) => parseInt(val));

// Refinements
z.number().refine((val) => val > 0, "Must be positive");

z.object({
  password: z.string(),
  confirmPassword: z.string(),
}).refine((data) => data.password === data.confirmPassword, {
  message: "Passwords must match",
  path: ["confirmPassword"],
});

// Discriminated unions
z.discriminatedUnion("type", [
  z.object({ type: z.literal("email"), email: z.string().email() }),
  z.object({ type: z.literal("phone"), phone: z.string() }),
]);

// Recursive types
type Category = {
  name: string;
  subcategories: Category[];
};

const categorySchema: z.ZodType<Category> = z.lazy(() =>
  z.object({
    name: z.string(),
    subcategories: z.array(categorySchema),
  })
);
```

---

## Logger Quick Patterns

```typescript
import { logger } from "@langfuse/shared/src/server";

// Log levels
logger.debug("Debug message", { context: "data" });
logger.info("Info message", { userId, action: "login" });
logger.warn("Warning message", { issue: "deprecated API used" });
logger.error("Error message", { error: err.message, stack: err.stack });

// Structured logging
logger.info("Dataset created", {
  datasetId,
  projectId,
  itemCount: 10,
  duration: Date.now() - startTime,
});

// With errors
try {
  await operation();
} catch (error) {
  logger.error("Operation failed", {
    operation: "dataset.create",
    error: error instanceof Error ? error.message : String(error),
    projectId,
  });
  traceException(error); // Also send to OpenTelemetry
  throw error;
}
```

---

## Example Features to Reference

Reference existing Langfuse features for implementation patterns:

| Feature | Location | Good For |
|---------|----------|----------|
| **Datasets** | `web/src/features/datasets/` | Complete feature with tRPC + Public API |
| **Prompts** | `web/src/features/prompts/` | Versioning and templates |
| **Evaluations** | `web/src/features/evals/` | Worker integration |
| **Public API** | `web/src/features/public-api/` | Middleware and route patterns |
| **Traces** | `web/src/features/traces/` | Complex queries with ClickHouse |
| **Scores** | `web/src/features/scores/` | Repository patterns |

---

## Queue Job Patterns

```typescript
// Publish job to queue
import { QueueName, redis } from "@langfuse/shared/src/server";

await redis?.queue.add(
  QueueName.EvaluationExecution,
  {
    projectId,
    jobExecutionId,
    evaluatorId,
  },
  {
    jobId: `eval-${jobExecutionId}`,
    removeOnComplete: true,
    removeOnFail: 1000,
  }
);

// Process queue job (Worker)
import { Job, Worker } from "bullmq";

const worker = new Worker(
  QueueName.EvaluationExecution,
  async (job: Job) => {
    const { projectId, jobExecutionId, evaluatorId } = job.data;
    
    try {
      await processEvaluation({ projectId, jobExecutionId, evaluatorId });
      return { success: true };
    } catch (error) {
      logger.error("Job failed", { jobId: job.id, error });
      throw error; // Will retry based on job options
    }
  },
  {
    connection: redis,
    concurrency: 10,
  }
);
```

---

## Environment Variable Patterns

```typescript
// Web (env.mjs)
import { env } from "@/src/env.mjs";

const isCloud = env.NEXT_PUBLIC_LANGFUSE_CLOUD_REGION !== undefined;
const hasEELicense = env.LANGFUSE_EE_LICENSE_KEY !== undefined;

// Feature flags
if (env.LANGFUSE_ENABLE_EXPERIMENTAL_FEATURES === "true") {
  // Enable experimental features
}

// Worker (env.ts)
import { env } from "./env";

const batchSize = env.LANGFUSE_WORKER_BATCH_SIZE ?? 1000;
const timeout = env.LANGFUSE_WORKER_TIMEOUT_MS ?? 30000;
```

---

## Common Middleware Patterns

```typescript
// tRPC middleware - enforce project access
const enforceProjectAccess = t.middleware(async ({ ctx, next, rawInput }) => {
  const input = rawInput as { projectId: string };
  
  if (!input.projectId) {
    throw new TRPCError({ code: "BAD_REQUEST", message: "projectId required" });
  }
  
  // Check user has access to project
  const hasAccess = await checkProjectAccess(ctx.session.user.id, input.projectId);
  
  if (!hasAccess) {
    throw new TRPCError({ code: "FORBIDDEN" });
  }
  
  return next();
});

// Public API middleware - rate limiting
const rateLimiter = async (req: NextApiRequest, res: NextApiResponse) => {
  const apiKey = req.headers.authorization;
  
  const { success } = await rateLimit.limit(apiKey);
  
  if (!success) {
    res.status(429).json({ error: "Rate limit exceeded" });
    return false;
  }
  
  return true;
};
```

---

## Anti-Patterns Checklist

Before committing, check:

- ❌ Business logic in routes/procedures → ✅ Move to services
- ❌ Direct `process.env` usage → ✅ Use `env.mjs` / `env.ts`
- ❌ Missing input validation → ✅ Add Zod schemas
- ❌ Missing `projectId` filter → ✅ Always filter by projectId
- ❌ `console.log` for errors → ✅ Use logger/traceException
- ❌ No tests → ✅ Add tests for new features
- ❌ Hardcoded values → ✅ Use config/constants
- ❌ Complex queries in services → ✅ Use repositories

---

## Running Tests

```bash
# Web package (Jest)
npm run test
npm run test:watch
npm run test:coverage

# Worker package (Vitest)
npm run test
npm run test:ui
npm run test:coverage

# Specific test file
npm test -- path/to/test.test.ts
```

---

## Useful Commands

```bash
# Prisma
npx prisma generate          # Generate Prisma Client
npx prisma migrate dev       # Create and apply migration
npx prisma studio            # Open Prisma Studio
npx prisma db seed           # Run seed script

# Type checking
npm run type-check           # Check TypeScript errors

# Linting
npm run lint                 # Run ESLint
npm run lint:fix             # Fix auto-fixable issues

# Build
npm run build                # Build for production
npm run dev                  # Development server
```
