# Common Imports

Danh sách các imports thường dùng trong backend development với Langfuse stack.

---

## tRPC (Web Package)

```typescript
// Zod validation (v4)
import { z } from "zod/v4";

// tRPC setup
import {
  createTRPCRouter,
  protectedProjectProcedure,
  protectedProcedure,
  publicProcedure,
} from "@/src/server/api/trpc";

// tRPC errors
import { TRPCError } from "@trpc/server";

// tRPC types
import type { inferRouterInputs, inferRouterOutputs } from "@trpc/server";
```

---

## Database

```typescript
// Prisma client
import { prisma } from "@langfuse/shared/src/db";

// Prisma types
import { Prisma } from "@prisma/client";
import type { Prisma } from "@prisma/client";

// Prisma enums
import { JobExecutionStatus, ObservationType } from "@prisma/client";

// Database type (for ClickHouse compatibility)
import { type DB as Database } from "@langfuse/shared";
```

---

## ClickHouse

```typescript
// ClickHouse queries
import {
  queryClickhouse,
  queryClickhouseStream,
  upsertClickhouse,
} from "@langfuse/shared/src/server";

// ClickHouse tables
import {
  getTracesTable,
  getObservationsTable,
  getScoresTable,
} from "@langfuse/shared/src/server";

// ClickHouse types
import type { TraceRecordReadType } from "@langfuse/shared/src/server";
```

---

## Observability - OpenTelemetry + DataDog

**Note**: Backend uses OpenTelemetry (NOT Sentry). Frontend uses Sentry.

```typescript
// Logging với OpenTelemetry/DataDog trace context
import { logger } from "@langfuse/shared/src/server";

// Record exceptions to OpenTelemetry spans
import { traceException } from "@langfuse/shared/src/server";

// Create instrumented spans for operations
import { instrumentAsync } from "@langfuse/shared/src/server";

// Usage examples
logger.info("Processing dataset", { datasetId, projectId });
logger.error("Failed operation", { error: err.message });

try {
  await operation();
} catch (error) {
  traceException(error);
  throw error;
}

const result = await instrumentAsync(
  { name: "dataset.create" },
  async (span) => {
    span.setAttributes({ datasetId, projectId });
    return dataset;
  }
);
```

---

## Configuration

```typescript
// Web package
import { env } from "@/src/env.mjs";

// Worker package
import { env } from "./env";

// Shared package
import { env } from "@langfuse/shared";

// Usage
const dbUrl = env.DATABASE_URL;
const redisUrl = env.REDIS_CONNECTION_STRING;
const cloudRegion = env.NEXT_PUBLIC_LANGFUSE_CLOUD_REGION;
```

---

## Public API (Web Package)

```typescript
// Middleware
import { withMiddlewares } from "@/src/features/public-api/server/withMiddlewares";
import { createAuthedProjectAPIRoute } from "@/src/features/public-api/server/createAuthedProjectAPIRoute";

// Types
import type { 
  GetDatasetV1Response,
  PostDatasetsV1Response,
} from "@/src/features/public-api/types/datasets";

// Validation
import {
  GetDatasetV1Query,
  PostDatasetsV1Body,
} from "@/src/features/public-api/server/datasets";
```

---

## Queue Processing (Worker Package)

```typescript
// BullMQ
import { Job, Queue, Worker } from "bullmq";

// Queue types
import { 
  QueueName, 
  TQueueJobTypes,
  EvalExecutionQueue,
  IngestionQueue,
} from "@langfuse/shared/src/server";

// Redis
import { redis } from "@langfuse/shared/src/server";
```

---

## Authentication

```typescript
// NextAuth.js (Web)
import { getServerAuthSession } from "@/src/server/auth";
import type { Session } from "next-auth";

// API Keys (Shared)
import { 
  verifyAuthHeaderAndReturnScope,
  invalidateApiKeysForProject,
} from "@langfuse/shared/src/server";

// Basic Auth helpers
import { 
  createAndAddApiKeysToDb,
} from "@langfuse/shared/src/server/auth/apiKeys";
```

---

## Shared Utilities

```typescript
// Services
import {
  StorageService,
  sendMembershipInvitationEmail,
} from "@langfuse/shared/src/server";

// Utilities
import {
  recordIncrement,
  recordHistogram,
  recordGauge,
} from "@langfuse/shared/src/server";

// Constants
import {
  Role,
  ApiAccessScope,
  availableEvalVariables,
} from "@langfuse/shared";

// Schemas
import {
  CloudConfigSchema,
  StringNoHTML,
  AnnotationQueueObjectType,
} from "@langfuse/shared";
```

---

## Encryption

```typescript
import { 
  encrypt, 
  decrypt, 
  sign, 
  verify 
} from "@langfuse/shared/encryption";

// Usage
const encrypted = encrypt("sensitive-data");
const decrypted = decrypt(encrypted);
```

---

## Testing

```typescript
// Jest (Web package)
import { describe, it, expect, beforeEach, afterEach } from "@jest/globals";

// Testing utilities
import { 
  makeZodVerifiedAPICall,
  pruneDatabase,
} from "@/src/__tests__/test-utils";

// Vitest (Worker package)
import { describe, it, expect, beforeEach, afterEach } from "vitest";
```

---

## Express (Worker Package)

```typescript
import express, { 
  Request, 
  Response, 
  NextFunction, 
  Router 
} from "express";

import type { Express, RequestHandler } from "express";
```

---

## Next.js (Web Package)

```typescript
// API routes
import type { NextApiRequest, NextApiResponse } from "next";

// App Router
import { headers } from "next/headers";
import { redirect } from "next/navigation";

// Pages Router
import { GetServerSideProps, GetStaticProps } from "next";
```

---

## TypeScript Utilities

```typescript
// Type helpers
import type { inferAsyncReturnType } from "@trpc/server";
import type { Awaited, Partial, Required, Pick, Omit } from "typescript";

// Utility types
type Nullable<T> = T | null;
type Optional<T> = T | undefined;
type DeepPartial<T> = { [P in keyof T]?: DeepPartial<T[P]> };
```

---

## Date/Time

```typescript
// date-fns
import { 
  format, 
  formatDistance, 
  parseISO,
  startOfDay,
  endOfDay,
} from "date-fns";

// Usage
const formatted = format(new Date(), "yyyy-MM-dd HH:mm:ss");
const distance = formatDistance(new Date(), pastDate);
```

---

## Import Path Reference

| Package Location | Import From | Purpose |
|------------------|-------------|---------|
| **General types/schemas** | `@langfuse/shared` | Prisma types, Zod schemas, constants |
| **Database client** | `@langfuse/shared/src/db` | Prisma client instance |
| **Server utilities** | `@langfuse/shared/src/server` | Services, repositories, queues, auth |
| **API key management** | `@langfuse/shared/src/server/auth/apiKeys` | API key operations |
| **Encryption** | `@langfuse/shared/encryption` | Encrypt/decrypt utilities |
| **Web config** | `@/src/env.mjs` | Environment variables (web) |
| **Worker config** | `./env` | Environment variables (worker) |

---

## Import Organization Best Practices

### Group imports by category

```typescript
// 1. External libraries
import { z } from "zod/v4";
import { format } from "date-fns";

// 2. Next.js / React
import type { NextApiRequest, NextApiResponse } from "next";

// 3. tRPC
import { createTRPCRouter, protectedProjectProcedure } from "@/src/server/api/trpc";

// 4. Database
import { prisma } from "@langfuse/shared/src/db";

// 5. Shared utilities
import { logger, traceException } from "@langfuse/shared/src/server";

// 6. Local imports
import { getDatasetById } from "./datasetService";
import type { CreateDatasetInput } from "./types";
```

### Use type imports when possible

```typescript
// ✅ Better for tree-shaking
import type { Prisma } from "@prisma/client";
import type { NextApiRequest } from "next";

// ❌ Avoid when you only need types
import { Prisma } from "@prisma/client"; // Imports runtime code
```

### Prefer named imports

```typescript
// ✅ Clear what you're using
import { logger, traceException } from "@langfuse/shared/src/server";

// ❌ Avoid wildcard imports
import * as Server from "@langfuse/shared/src/server"; // Not clear what's used
```
