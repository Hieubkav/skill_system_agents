# Query Implementation Patterns

## Basic Query Pattern
```typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

export const getMessagesByChannel = query({
  args: { 
    channelId: v.id("channels"),
    paginationOpts: paginationOptsValidator 
  },
  handler: async (ctx, args) => {
    return await ctx.db
      .query("messages")
      .withIndex("by_channel", (q) => q.eq("channelId", args.channelId))
      .order("desc")
      .paginate(args.paginationOpts);
  },
});
```

## Pagination Pattern
```typescript
export const listPaginatedItems = query({
  args: { 
    paginationOpts: paginationOptsValidator 
  },
  handler: async (ctx, args) => {
    return await ctx.db
      .query("items")
      .order("desc")
      .paginate(args.paginationOpts);
  },
});

// Usage in React:
const { results, status, loadMore } = usePaginatedQuery(
  api.queries.listPaginatedItems,
  {},
  { initialNumItems: 20 }
);
```

## Index Optimization
```typescript
// Schema setup
const schema = defineSchema({
  messages: defineTable({
    channelId: v.id("channels"),
    authorId: v.id("users"),
    content: v.string(),
    timestamp: v.number(),
  })
  .index("by_channel", ["channelId"])
  .index("by_author_time", ["authorId", "timestamp"]),
});

// Query with index
export const getUserMessages = query({
  args: { userId: v.id("users") },
  handler: async (ctx, { userId }) => {
    return await ctx.db
      .query("messages")
      .withIndex("by_author_time", (q) => q.eq("authorId", userId))
      .order("desc")
      .take(50);
  },
});
```

## Full Text Search
```typescript
// Schema with search index
const schema = defineSchema({
  documents: defineTable({
    title: v.string(),
    content: v.string(),
  })
  .searchIndex("search_content", {
    searchField: "content",
    filterField: "title",
  }),
});

// Search query
export const searchDocuments = query({
  args: { query: v.string() },
  handler: async (ctx, { query }) => {
    return await ctx.db
      .query("documents")
      .withSearchIndex("search_content", q.search(query))
      .take(10);
  },
});
```

## Query Optimization Tips
- **Never use filter()**: Always use withIndex() for performance
- **Limit results**: Use .take() or pagination for large datasets
- **Order efficiently**: Use indexed fields for .order()
- **Return types**: Add explicit return types in same file to avoid circular references
- **Timeout aware**: Queries execute max 1 second, can read 16384 documents

## Advanced Patterns
```typescript
// Compound query with multiple conditions
export const getActiveUserMessages = query({
  args: { 
    channelId: v.id("channels"),
    activeOnly: v.boolean() 
  },
  handler: async (ctx, args) => {
    let baseQuery = ctx.db
      .query("messages")
      .withIndex("by_channel", (q) => q.eq("channelId", args.channelId));
    
    if (args.activeOnly) {
      baseQuery = baseQuery.filter((msg) => msg.active);
    }
    
    return await baseQuery.order("desc").take(20);
  },
});
```
