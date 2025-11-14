---
name: convex-core
description: Xử lý Convex backend: queries, mutations, actions, HTTP endpoints, schema design, validators, authentication, file storage, React hooks, scheduling. Dùng khi làm việc với Convex backend, database operations, function definitions, API endpoints.
---

# Convex Core Skill

## Level 1: Overview

Hướng dẫn toàn diện triển khai backend với Convex - từ database schema, queries/mutations/actions, đến frontend integration với React hooks.

### Skill này làm gì?
- Implement Convex queries, mutations, actions cho backend development
- Setup database schemas, indexes, validators, và relationships
- Configure HTTP endpoints, authentication, file storage
- Integrate React hooks (useQuery, useMutation) cho frontend

### Yêu cầu gì?
- Convex project đã setup (`npx create-convex-app` hoặc existing project)
- Biết TypeScript basics
- Hiểu database concepts (tables, documents, indexes)

### Output ra sao?
- Complete Convex backend code patterns
- Database schemas với validators
- Function templates (queries, mutations, actions)
- Best practices cho performance và security

---

## Level 2: Quick Start

### Bước 1: Setup Schema
```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  messages: defineTable({
    channelId: v.id("channels"),
    content: v.string(),
    createdAt: v.number(),
  }).index("by_channel", ["channelId"]),
  channels: defineTable({
    name: v.string(),
  }),
});
```

### Bước 2: Implement Query (đọc dữ liệu)
```typescript
// convex/messages.ts
import { query } from "./_generated/server";
import { v } from "convex/values";

export const getMessages = query({
  args: { channelId: v.id("channels") },
  handler: async (ctx, args) => {
    return await ctx.db
      .query("messages")
      .withIndex("by_channel", (q) => q.eq("channelId", args.channelId))
      .order("desc")
      .take(50);
  },
});
```

### Bước 3: Implement Mutation (ghi dữ liệu)
```typescript
// convex/messages.ts
import { mutation } from "./_generated/server";

export const sendMessage = mutation({
  args: { channelId: v.id("channels"), content: v.string() },
  handler: async (ctx, args) => {
    const messageId = await ctx.db.insert("messages", {
      ...args,
      createdAt: Date.now(),
    });
    return messageId;
  },
});
```

### Bước 4: Implement Action (tác vụ bên ngoài)
```typescript
// convex/ai.ts
import { action } from "./_generated/server";
import { v } from "convex/values";

export const generateResponse = action({
  args: { prompt: v.string() },
  handler: async (ctx, { prompt }) => {
    // Call external API (OpenAI, Stripe, etc.)
    const response = await fetch("https://api.openai.com/...");
    return await response.json();
  },
});
```

### Bước 5: Frontend Integration
```typescript
// src/App.tsx
import { useQuery, useMutation } from "convex/react";
import { api } from "../convex/_generated/api";

export function Chat() {
  const messages = useQuery(api.messages.getMessages, { channelId: "..." });
  const sendMessage = useMutation(api.messages.sendMessage);

  return (
    <div>
      {messages?.map(msg => <div key={msg._id}>{msg.content}</div>)}
      <button onClick={() => sendMessage({ channelId: "...", content: "Hi" })}>
        Send
      </button>
    </div>
  );
}
```

### Best Practices
- **Validators**: Luôn dùng `v.*` validators cho arguments
- **Queries**: KHÔNG dùng `.filter()`, dùng `.withIndex()` để optimize
- **Mutations**: Giữ atomic, max ~1 second timeout
- **Actions**: Dùng `"use node"` cho npm packages, `fetch` built-in
- **Indexes**: Tạo cho fields dùng thường xuyên trong `.withIndex()`

---

## Level 3: References

### Core Functionality
- [queries.md](./references/queries.md) - Query patterns, pagination, indexing
- [mutations.md](./references/mutations.md) - Database operations, batch updates, transactions
- [actions.md](./references/actions.md) - HTTP endpoints, external APIs, scheduling

### Data & Schema
- [schema.md](./references/schema.md) - Schema design, validators, relationships
- [config.md](./references/config.md) - Environment variables, secrets management
- [files.md](./references/files.md) - File storage upload/download workflows

### Frontend & Auth
- [react.md](./references/react.md) - React hooks, client integration
- [auth.md](./references/auth.md) - Authentication, authorization patterns

### Advanced
- [performance.md](./references/performance.md) - Query optimization, indexing strategies
- [errors.md](./references/errors.md) - Error handling, recovery patterns
- [testing.md](./references/testing.md) - Testing setup, debugging approaches
