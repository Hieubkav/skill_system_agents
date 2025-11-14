# Directory Structure

Chi tiết cấu trúc thư mục cho backend development với Node.js/Express/TypeScript.

---

## Web Package (`/web/`)

```
web/src/
├── features/                # Feature-organized code
│   ├── [feature-name]/
│   │   ├── server/          # Backend logic
│   │   │   ├── *Router.ts   # tRPC router
│   │   │   └── service.ts   # Business logic
│   │   ├── components/      # React components
│   │   └── types/           # Feature types
├── server/
│   ├── api/
│   │   ├── routers/         # tRPC routers
│   │   ├── trpc.ts          # tRPC setup & middleware
│   │   └── root.ts          # Main router
│   ├── auth.ts              # NextAuth.js config
│   └── db.ts                # Database client
├── pages/
│   ├── api/
│   │   ├── public/          # Public REST APIs
│   │   └── trpc/            # tRPC endpoint
│   └── [routes].tsx         # Next.js pages
├── __tests__/               # Jest tests
│   └── async/               # Integration tests
├── instrumentation.ts       # OpenTelemetry (FIRST IMPORT)
└── env.mjs                  # Environment config
```

---

## Worker Package (`/worker/`)

```
worker/src/
├── queues/                  # BullMQ processors
│   ├── evalQueue.ts
│   ├── ingestionQueue.ts
│   └── workerManager.ts
├── features/                # Business logic
│   └── [feature]/
│       └── service.ts
├── instrumentation.ts       # OpenTelemetry (FIRST IMPORT)
├── app.ts                   # Express setup + queue registration
├── env.ts                   # Environment config
└── index.ts                 # Server start
```

---

## Shared Package (`/packages/shared/`)

```
shared/src/
├── server/                  # Server utilities
│   ├── auth/                # Authentication helpers
│   ├── clickhouse/          # ClickHouse client & schema
│   ├── instrumentation/     # OpenTelemetry helpers
│   ├── llm/                 # LLM integration utilities
│   ├── redis/               # Redis queues & cache
│   ├── repositories/        # Data repositories
│   ├── services/            # Shared services
│   ├── utils/               # Server utilities
│   ├── logger.ts
│   └── queues.ts
├── encryption/              # Encryption utilities
├── features/                # Feature-specific code
├── tableDefinitions/        # Table schemas
├── utils/                   # Shared utilities
├── constants.ts
├── db.ts                    # Prisma client
├── env.ts                   # Environment config
└── index.ts                 # Main exports
```

---

## Import Paths (package.json exports)

The shared package exposes specific import paths for different use cases:

| Import Path                                | Maps To                           | Use For                                                         |
| ------------------------------------------ | --------------------------------- | --------------------------------------------------------------- |
| `@langfuse/shared`                         | `dist/src/index.js`               | General types, schemas, utilities, constants                    |
| `@langfuse/shared/src/db`                  | `dist/src/db.js`                  | Prisma client and database types                                |
| `@langfuse/shared/src/server`              | `dist/src/server/index.js`        | Server-side utilities (queues, auth, services, instrumentation) |
| `@langfuse/shared/src/server/auth/apiKeys` | `dist/src/server/auth/apiKeys.js` | API key management utilities                                    |
| `@langfuse/shared/encryption`              | `dist/src/encryption/index.js`    | Encryption and signature utilities                              |

---

## Usage Examples

```typescript
// General imports - types, schemas, constants, interfaces
import {
  CloudConfigSchema,
  StringNoHTML,
  AnnotationQueueObjectType,
  type APIScoreV2,
  type ColumnDefinition,
  Role,
} from "@langfuse/shared";

// Database - Prisma client and types
import { prisma, Prisma, JobExecutionStatus } from "@langfuse/shared/src/db";
import { type DB as Database } from "@langfuse/shared";

// Server utilities - queues, services, auth, instrumentation
import {
  logger,
  instrumentAsync,
  traceException,
  redis,
  getTracesTable,
  StorageService,
  sendMembershipInvitationEmail,
  invalidateApiKeysForProject,
  recordIncrement,
  recordHistogram,
} from "@langfuse/shared/src/server";

// API key management (specific path)
import { createAndAddApiKeysToDb } from "@langfuse/shared/src/server/auth/apiKeys";

// Encryption utilities
import { encrypt, decrypt, sign, verify } from "@langfuse/shared/encryption";
```

---

## What Goes Where

The shared package provides types, utilities, and server code used by both web and worker packages. It has **5 export paths** that control frontend vs backend access:

| Import Path                                | Usage                 | What's Included                                                                    |
| ------------------------------------------ | --------------------- | ---------------------------------------------------------------------------------- |
| `@langfuse/shared`                         | ✅ Frontend + Backend | Prisma types, Zod schemas, constants, table definitions, domain models, utilities  |
| `@langfuse/shared/src/db`                  | 🔒 Backend only       | Prisma client instance                                                             |
| `@langfuse/shared/src/server`              | 🔒 Backend only       | Services, repositories, queues, auth, ClickHouse, LLM integration, instrumentation |
| `@langfuse/shared/src/server/auth/apiKeys` | 🔒 Backend only       | API key management (separated to avoid circular deps)                              |
| `@langfuse/shared/encryption`              | 🔒 Backend only       | Database field encryption/decryption                                               |

---

## Naming Conventions

- **tRPC Routers**: `camelCaseRouter.ts` - `datasetRouter.ts`
- **Services**: `service.ts` in feature directory
- **Queue Processors**: `camelCaseQueue.ts` - `evalQueue.ts`
- **Public APIs**: `kebab-case.ts` - `dataset-items.ts`
- **Controllers**: `PascalCase` - `UserController.ts`
- **Repositories**: `PascalCase + Repository` - `UserRepository.ts`
- **Routes**: `camelCase + Routes` - `userRoutes.ts`

---

## File Organization Best Practices

### Feature-First Organization

Group related code by feature/domain, not by technical role:

```
✅ GOOD - Feature-first
features/
├── datasets/
│   ├── server/
│   │   ├── datasetRouter.ts
│   │   └── datasetService.ts
│   ├── components/
│   └── types/
└── prompts/
    ├── server/
    └── components/

❌ BAD - Technical role first
src/
├── routers/
│   ├── datasetRouter.ts
│   └── promptRouter.ts
├── services/
│   ├── datasetService.ts
│   └── promptService.ts
```

### Keep Files Focused

- Each file should have ONE primary responsibility
- Aim for < 300 lines per file
- Split large files into smaller modules

### Co-locate Related Code

- Tests near implementation (`__tests__/` in same directory)
- Types near usage (feature `types/` folder)
- Utilities near consumers (feature `utils/` folder)
