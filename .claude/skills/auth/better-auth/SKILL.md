---
name: better-auth
description: Production-ready authentication for TypeScript with Cloudflare D1 support via Drizzle ORM or Kysely. Use this skill when building auth systems as a self-hosted alternative to Clerk or Auth.js, particularly for Cloudflare Workers projects. CRITICAL: better-auth requires Drizzle ORM or Kysely as database adapters - there is NO direct D1 adapter. Supports social providers, email/password, magic links, 2FA, passkeys, organizations, and RBAC.
license: MIT
metadata:
  version: 2.0.0
  last_verified: 2025-11-08
  production_tested: true
  package_version: 1.3.34
  token_savings: ~70%
  errors_prevented: 12
  official_docs: https://better-auth.com
  github: https://github.com/better-auth/better-auth
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
---

# better-auth Skill

## Overview

**better-auth** is a comprehensive TypeScript authentication library supporting Cloudflare D1 via **Drizzle ORM** or **Kysely** (NO direct d1Adapter exists).

### Use this skill when:
- Building auth for Cloudflare Workers + D1 applications
- Need self-hosted alternative to Clerk/Auth.js
- Require 2FA, passkeys, organizations, RBAC
- Want vendor-independent auth solution

**Package**: `better-auth@1.3.34`

---

## Quick Start: Cloudflare Workers + D1 + Drizzle

### 1. Install Dependencies
```bash
npm install better-auth drizzle-orm drizzle-kit @cloudflare/workers-types hono
```

### 2. Define Database Schema
**File**: `src/db/schema.ts`
```typescript
import { integer, sqliteTable, text } from "drizzle-orm/sqlite-core";
import { sql } from "drizzle-orm";

export const user = sqliteTable("user", {
  id: text().primaryKey(),
  name: text().notNull(),
  email: text().notNull().unique(),
  emailVerified: integer({ mode: "boolean" }).notNull().default(false),
  image: text(),
  createdAt: integer({ mode: "timestamp" }).notNull().default(sql`(unixepoch())`),
  updatedAt: integer({ mode: "timestamp" }).notNull().default(sql`(unixepoch())`),
});

export const session = sqliteTable("session", {
  id: text().primaryKey(),
  userId: text().notNull().references(() => user.id, { onDelete: "cascade" }),
  token: text().notNull(),
  expiresAt: integer({ mode: "timestamp" }).notNull(),
  ipAddress: text(),
  userAgent: text(),
  createdAt: integer({ mode: "timestamp" }).notNull().default(sql`(unixepoch())`),
  updatedAt: integer({ mode: "timestamp" }).notNull().default(sql`(unixepoch())`),
});

export const account = sqliteTable("account", {
  id: text().primaryKey(),
  userId: text().notNull().references(() => user.id, { onDelete: "cascade" }),
  accountId: text().notNull(),
  providerId: text().notNull(),
  accessToken: text(),
  refreshToken: text(),
  accessTokenExpiresAt: integer({ mode: "timestamp" }),
  refreshTokenExpiresAt: integer({ mode: "timestamp" }),
  scope: text(),
  idToken: text(),
  password: text(),
  createdAt: integer({ mode: "timestamp" }).notNull().default(sql`(unixepoch())`),
  updatedAt: integer({ mode: "timestamp" }).notNull().default(sql`(unixepoch())`),
});

export const verification = sqliteTable("verification", {
  id: text().primaryKey(),
  identifier: text().notNull(),
  value: text().notNull(),
  expiresAt: integer({ mode: "timestamp" }).notNull(),
  createdAt: integer({ mode: "timestamp" }).notNull().default(sql`(unixepoch())`),
  updatedAt: integer({ mode: "timestamp" }).notNull().default(sql`(unixepoch())`),
});
```

### 3. Initialize Database
**File**: `src/db/index.ts`
```typescript
import { drizzle } from "drizzle-orm/d1";
import * as schema from "./schema";

export type Database = ReturnType<typeof createDatabase>;

export function createDatabase(d1: D1Database) {
  return drizzle(d1, { schema });
}
```

### 4. Configure Auth
**File**: `src/auth.ts`
```typescript
import { betterAuth } from "better-auth";
import { drizzleAdapter } from "better-auth/adapters/drizzle";
import type { Database } from "./db";

type Env = {
  DB: D1Database;
  BETTER_AUTH_SECRET: string;
  BETTER_AUTH_URL: string;
};

export function createAuth(db: Database, env: Env) {
  return betterAuth({
    baseURL: env.BETTER_AUTH_URL,
    secret: env.BETTER_AUTH_SECRET,
    database: drizzleAdapter(db, { provider: "sqlite" }),
    emailAndPassword: { enabled: true },
  });
}
```

### 5. Setup Worker
**File**: `src/index.ts`
```typescript
import { Hono } from "hono";
import { cors } from "hono/cors";
import { createDatabase } from "./db";
import { createAuth } from "./auth";

type Env = {
  DB: D1Database;
  BETTER_AUTH_SECRET: string;
  BETTER_AUTH_URL: string;
};

const app = new Hono<{ Bindings: Env }>();

app.use("/api/*", cors({ origin: "*", credentials: true }));

app.all("/api/auth/*", async (c) => {
  const db = createDatabase(c.env.DB);
  const auth = createAuth(db, c.env);
  return auth.handler(c.req.raw);
});

app.get("/health", (c) => c.json({ status: "ok" }));

export default app;
```

---

## Common Issues & Solutions

Refer to full SKILL documentation for:
- D1 adapter misconfiguration
- Schema generation failures
- Session serialization errors
- CORS configuration for SPAs
- OAuth redirect URI mismatches
- Email verification setup
- Session expiration tuning
- Social provider scope issues
- CamelCase vs snake_case column mismatches
- D1 eventual consistency handling

See `references/` directory for detailed guides and complete examples.

---

## Level 3: References

- [cloudflare-worker-drizzle.ts](./references/cloudflare-worker-drizzle.ts) - Complete Worker implementation
- [cloudflare-worker-kysely.ts](./references/cloudflare-worker-kysely.ts) - Kysely alternative pattern
- [database-schema.ts](./references/database-schema.ts) - Full schema definitions
- [react-client-hooks.tsx](./references/react-client-hooks.tsx) - Frontend integration
- [auth-flow-diagram.md](./assets/auth-flow-diagram.md) - Visual flows

---

## Resources

- **Official Docs**: https://better-auth.com
- **GitHub**: https://github.com/better-auth/better-auth (22.4k ⭐)
- **Examples**: https://github.com/better-auth/better-auth/tree/main/examples
- **Drizzle Docs**: https://orm.drizzle.team/docs/get-started-sqlite

---

**Last verified**: 2025-11-08 | **v2.0.0** | **Token savings**: ~70%
