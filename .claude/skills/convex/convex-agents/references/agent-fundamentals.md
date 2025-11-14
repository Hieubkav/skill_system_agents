# Agent Setup Fundamentals

## Basic Agent Configuration
```typescript
// convex/agents/myAgent.ts
import { components } from "../_generated/api";
import { Agent } from "@convex-dev/agent";
import { openai } from "@ai-sdk/openai";

export const myAgent = new Agent(components.agent, {
  name: "Chat Assistant",
  languageModel: openai.chat("gpt-4o-mini"),
  instructions: "You are a helpful assistant. Be concise and friendly.",
});
```

## Agent Component Setup
```typescript
// convex/convex.config.ts
import { defineApp } from "convex/server";
import agent from "@convex-dev/agent/convex.config";

const app = defineApp();
app.use(agent);

export default app;

// Run: npx convex dev
```

## Thread Management
```typescript
// convex/threads.ts
export const createThread = action({
  args: { 
    userId: v.string(),
    title: v.optional(v.string()),
  },
  handler: async (ctx, { userId, title }) => {
    const { thread } = await myAgent.createThread(ctx, {
      userId,
      title: title || "New Chat",
    });
    
    return { 
      threadId: thread.getMetadata().threadId 
    };
  },
});

export const continueThread = action({
  args: { 
    threadId: v.string(),
    prompt: v.string() 
  },
  handler: async (ctx, { threadId, prompt }) => {
    const { thread } = await myAgent.continueThread(ctx, { threadId });
    const result = await thread.generateText({ prompt });
    return result.text;
  },
});
```

## Structured Generation
```typescript
export const generateObject = action({
  args: { 
    threadId: v.string(),
    request: v.string() 
  },
  handler: async (ctx, { threadId, request }) => {
    const { thread } = await myAgent.continueThread(ctx, { threadId });
    
    const result = await thread.generateObject({
      prompt: request,
      schema: z.object({
        response: z.string(),
        confidence: z.number().min(0).max(1),
        needsEscalation: z.boolean(),
      }),
    });
    
    return result.object;
  },
});
```

## React Integration
```typescript
// frontend/ChatComponent.tsx
import { useAction } from "convex/react";
import { api } from "../convex/_generated/api";

export function ChatInterface() {
  const createThread = useAction(api.threads.createThread);
  const sendMessage = useAction(api.threads.continueThread);
  
  const [threadId, setThreadId] = useState<string | null>(null);
  
  const handleNewChat = async () => {
    const result = await createThread({ 
      userId: "user123" 
    });
    setThreadId(result.threadId);
  };
  
  const handleMessage = async (message: string) => {
    if (!threadId) return;
    
    const response = await sendMessage({
      threadId,
      prompt: message,
    });
    
    return response;
  };
  
  return (
    <div>
      <button onClick={handleNewChat}>New Chat</button>
      {threadId && <ChatMessages threadId={threadId} />}
    </div>
  );
}
```

## Message Management
```typescript
// convex/messages.ts
export const saveUserMessage = mutation({
  args: { 
    threadId: v.string(), 
    prompt: v.string() 
  },
  handler: async (ctx, { threadId, prompt }) => {
    const { messageId } = await saveMessage(ctx, components.agent, {
      threadId,
      prompt,
      skipEmbeddings: true,
    });
    return { messageId };
  },
});

export const getThreadMessages = query({
  args: { 
    threadId: v.string(),
    paginationOpts: paginationOptsValidator 
  },
  handler: async (ctx, { threadId, paginationOpts }) => {
    return await listUIMessages(ctx, components.agent, {
      threadId,
      paginationOpts,
    });
  },
});
```

## Agent Configuration Options
```typescript
export const configuredAgent = new Agent(components.agent, {
  name: "Advanced Assistant",
  languageModel: openai.chat("gpt-4o-mini"),
  instructions: "You are an expert assistant...",
  
  // Context configuration
  contextOptions: {
    recentMessages: 20,
    excludeToolMessages: true,
    searchOptions: {
      limit: 5,
      textSearch: true,
      vectorSearch: true,
    },
  },
  
  // Request/response logging
  rawRequestResponseHandler: async (ctx, { request, response }) => {
    console.log("Agent interaction:", { request, response });
  },
});
```

## Error Handling
```typescript
export const robustChat = action({
  args: { threadId: v.string(), prompt: v.string() },
  handler: async (ctx, { threadId, prompt }) => {
    try {
      const { thread } = await myAgent.continueThread(ctx, { threadId });
      
      // Set timeout for long-running operations
      const result = await Promise.race([
        thread.generateText({ prompt }),
        new Promise((_, reject) => 
          setTimeout(() => reject(new Error("Timeout")), 30000)
        ),
      ]);
      
      return { success: true, text: result.text };
    } catch (error) {
      // Handle errors gracefully
      await ctx.runMutation(internal.errors.logAgentError, {
        threadId,
        error: error.message,
        timestamp: Date.now(),
      });
      
      return { 
        success: false, 
        error: "Sorry, I encountered an issue. Please try again." 
      };
    }
  },
});
```

## Testing with Playground
```typescript
// convex/playground.ts
export const playgroundChat = action({
  args: { prompt: v.string() },
  handler: async (ctx, { prompt }) => {
    // Create temporary thread for testing
    const { thread } = await myAgent.createThread(ctx, {
      userId: "playground",
      title: "Test Session",
    });

    // Test with different prompts
    const result = await thread.generateText({ prompt });
    
    // Return metrics for testing
    return {
      response: result.text,
      tokens: result.usage?.totalTokens,
      duration: result.duration,
    };
  },
});
```
