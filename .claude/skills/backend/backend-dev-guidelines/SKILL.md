---
name: backend-dev-guidelines
description: Comprehensive backend development guide for Langfuse's Next.js 14/tRPC/Express/TypeScript monorepo. Use when creating tRPC routers, public API endpoints, BullMQ queue processors, services, or working with tRPC procedures, Next.js API routes, Prisma database access, ClickHouse analytics queries, Redis queues, OpenTelemetry instrumentation, Zod v4 validation, env.mjs configuration, tenant isolation patterns, or async patterns. Covers layered architecture (tRPC procedures → services, queue processors → services), dual database system (PostgreSQL + ClickHouse), projectId filtering for multi-tenant isolation, traceException error handling, observability patterns, and testing strategies (Jest for web, vitest for worker).
---

# Backend Development Guidelines

## Level 1: Overview

Best practices cho Langfuse backend (web, worker, shared packages) với Next.js 14, tRPC, BullMQ, TypeScript.

### Skill này làm gì?

- **Architecture**: Layered architecture (tRPC/API → Services → Repositories → Database)
- **Database**: PostgreSQL (Prisma) + ClickHouse cho analytics
- **Observability**: OpenTelemetry + DataDog (logger, traceException, instrumentAsync)
- **Validation**: Zod v4 schemas cho tất cả inputs
- **Security**: Multi-tenant isolation với projectId filtering
- **Testing**: Jest (web) + Vitest (worker)

### Khi nào dùng?

Auto-activates khi làm việc với:
- tRPC routers, procedures, public API endpoints
- Services, repositories, middleware
- Database operations (Prisma, ClickHouse)
- Queue processors (BullMQ)
- Environment config (env.mjs/env.ts)
- Backend testing và refactoring

---

## Level 2: Quick Start

### Checklists

#### tRPC Feature (Web)
- [ ] Router trong `features/[feature]/server/*Router.ts`
- [ ] Procedures với auth (protectedProjectProcedure)
- [ ] Zod v4 validation
- [ ] Service cho business logic
- [ ] traceException cho errors
- [ ] Tests trong `__tests__/`

#### Public API Endpoint (Web)
- [ ] Route trong `pages/api/public/`
- [ ] Middleware: `withMiddlewares` + `createAuthedProjectAPIRoute`
- [ ] Zod schemas cho input/output
- [ ] Versioning trong path
- [ ] E2E tests trong `__tests__/async/`

#### Queue Processor (Worker)
- [ ] Processor trong `worker/src/queues/`
- [ ] Service cho business logic
- [ ] Error handling (fail vs success)
- [ ] Registration trong WorkerManager
- [ ] Vitest tests

### Layered Architecture

```
HTTP Request
    ↓
tRPC/API Routes (routing + validation)
    ↓
Services (business logic)
    ↓
Repositories (complex queries) / Prisma (simple CRUD)
    ↓
Database (PostgreSQL + ClickHouse)
```

**Chi tiết**: [architecture-overview.md](references/architecture-overview.md)

### 7 Core Principles

1. **Procedures Delegate to Services** - Business logic ở services, không phải routes/procedures
2. **Use env.mjs, NEVER process.env** - Validated config objects thay vì direct env access
3. **Validate ALL with Zod v4** - Mọi external input phải validate
4. **Prisma/Repositories Pattern** - Simple CRUD dùng Prisma, complex queries dùng repositories
5. **Always Filter by projectId** - Multi-tenant isolation, bắt buộc
6. **OpenTelemetry + DataDog** - logger, traceException, instrumentAsync (NOT Sentry)
7. **Comprehensive Testing** - Jest (web) + Vitest (worker) cho tất cả features

**Chi tiết & ví dụ**: [core-principles.md](references/core-principles.md)

### Anti-Patterns ❌

- Business logic trong routes/procedures
- Direct `process.env` usage
- Missing validation/error handling
- Missing `projectId` filter
- `console.log` thay vì logger

---

## Level 3: References

### Chi tiết Implementation

- [architecture-overview.md](references/architecture-overview.md) - Layered architecture, request lifecycle
- [directory-structure.md](references/directory-structure.md) - Cấu trúc thư mục Web/Worker/Shared packages
- [core-principles.md](references/core-principles.md) - 7 nguyên tắc quan trọng với ví dụ chi tiết
- [common-imports.md](references/common-imports.md) - Danh sách imports thường dùng
- [quick-reference.md](references/quick-reference.md) - HTTP codes, Prisma patterns, cheat sheets

---

### Guides chi tiết khác

| Cần làm gì | Đọc file |
|-----------|---------|
| Routes/Controllers patterns | [routing-and-controllers.md](references/routing-and-controllers.md) |
| Services/Repositories organization | [services-and-repositories.md](references/services-and-repositories.md) |
| Middleware implementation | [middleware-guide.md](references/middleware-guide.md) |
| Database access patterns | [database-patterns.md](references/database-patterns.md) |
| Environment configuration | [configuration.md](references/configuration.md) |
| Testing strategies | [testing-guide.md](references/testing-guide.md) |

### Example Features

Tham khảo features có sẵn:
- **Datasets** (`web/src/features/datasets/`) - Complete tRPC + Public API
- **Prompts** (`web/src/features/prompts/`) - Versioning patterns
- **Evaluations** (`web/src/features/evals/`) - Worker integration

---

**Skill Status**: ✅ COMPLETE
**Progressive Disclosure**: 11 reference files
