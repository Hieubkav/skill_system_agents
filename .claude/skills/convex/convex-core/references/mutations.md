# Mutation Implementation Patterns

## Basic Mutation Pattern
```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

export const sendMessage = mutation({
  args: {
    channelId: v.id("channels"),
    authorId: v.id("users"),
    content: v.string(),
  },
  handler: async (ctx, args) => {
    const messageId = await ctx.db.insert("messages", {
      channelId: args.channelId,
      authorId: args.authorId,
      content: args.content,
      timestamp: Date.now(),
    });

    return { messageId };
  },
});
```

## Database Operations
```typescript
// INSERT - Create new document
export const createUser = mutation({
  args: { name: v.string(), email: v.string() },
  handler: async (ctx, args) => {
    return await ctx.db.insert("users", args);
  },
});

// PATCH - Shallow merge update
export const updateUserInfo = mutation({
  args: { userId: v.id("users"), name: v.optional(v.string()) },
  handler: async (ctx, args) => {
    const { userId, ...updates } = args;
    return await ctx.db.patch(userId, updates);
  },
});

// REPLACE - Full document replacement
export const replaceUser = mutation({
  args: { userId: v.id("users"), user: v.object({ name: v.string(), email: v.string() }) },
  handler: async (ctx, args) => {
    return await ctx.db.replace(args.userId, args.user);
  },
});

// DELETE - Remove document
export const deleteUser = mutation({
  args: { userId: v.id("users") },
  handler: async (ctx, { userId }) => {
    await ctx.db.delete(userId);
    return { deleted: true };
  },
});
```

## Scheduling Pattern
```typescript
import { mutation } from "./_generated/server";
import { internal } from "./_generated/api";

export const sendMessageWithAI = mutation({
  args: {
    channelId: v.id("channels"),
    content: v.string(),
  },
  handler: async (ctx, args) => {
    // 1. Save user message
    await ctx.db.insert("messages", {
      channelId: args.channelId,
      role: "user",
      content: args.content,
      timestamp: Date.now(),
    });

    // 2. Schedule AI response (transactional)
    await ctx.scheduler.runAfter(0, internal.ai.generateResponse, {
      channelId: args.channelId,
    });

    return { sent: true };
  },
});

// Internal action for AI processing
export const generateResponse = internalAction({
  args: { channelId: v.id("channels") },
  handler: async (ctx, { channelId }) => {
    // Get context
    const recentMessages = await ctx.runQuery(
      internal.messages.getRecentMessages,
      { channelId, limit: 10 }
    );

    // Generate AI response
    const aiResponse = await openai.chat.completions.create({
      model: "gpt-4o-mini",
      messages: recentMessages,
    });

    // Save AI response
    await ctx.runMutation(internal.messages.saveAIMessage, {
      channelId,
      content: aiResponse.choices[0].message.content!,
    });
  },
});
```

## Error Handling & Validation
```typescript
export const createChannel = mutation({
  args: { name: v.string(), ownerId: v.id("users") },
  handler: async (ctx, args) => {
    // Validate user exists
    const user = await ctx.db.get(args.ownerId);
    if (!user) {
      throw new Error("User not found");
    }

    // Check for duplicate name
    const existing = await ctx.db
      .query("channels")
      .filter((c) => c.name === args.name && c.ownerId === args.ownerId)
      .first();
    
    if (existing) {
      throw new Error("Channel name already exists");
    }

    // Create channel
    const channelId = await ctx.db.insert("channels", {
      name: args.name,
      ownerId: args.ownerId,
      createdAt: Date.now(),
    });

    return { channelId };
  },
});
```

## Batch Operations
```typescript
export const batchUpdateMessages = mutation({
  args: {
    messageIds: v.array(v.id("messages")),
    updates: v.object({ pinned: v.optional(v.boolean()) }),
  },
  handler: async (ctx, { messageIds, updates }) => {
    // Note: mutations can batch writes up to 8192 documents
    const results = [];
    
    for (const messageId of messageIds) {
      const result = await ctx.db.patch(messageId, updates);
      results.push(result);
    }

    return { updated: results.length };
  },
});
```

## Transaction Patterns
```typescript
export const transferOwnership = mutation({
  args: {
    channelId: v.id("channels"),
    fromUserId: v.id("users"), 
    toUserId: v.id("users"),
  },
  handler: async (ctx, args) => {
    const channel = await ctx.db.get(args.channelId);
    if (!channel) throw new Error("Channel not found");
    if (channel.ownerId !== args.fromUserId) {
      throw new Error("Not the channel owner");
    }

    // Update channel ownership
    await ctx.db.patch(args.channelId, {
      ownerId: args.toUserId,
      transferredAt: Date.now(),
    });

    // Remove old owner if needed
    await ctx.runMutation(internal.users.removeFromChannel, {
      userId: args.fromUserId,
      channelId: args.channelId,
    });

    return { transferred: true };
  },
});
```

## Mutation Best Practices
- **Always validate** inputs and check document existence
- **Use batch operations** when updating multiple documents
- **Schedule long tasks** with `ctx.scheduler.runAfter()` 
- **Handle auth carefully** - auth state doesn't propagate to scheduled jobs
- **Return meaningful results** for client-side reactions
- **1 second timeout** - keep mutations fast and focused
