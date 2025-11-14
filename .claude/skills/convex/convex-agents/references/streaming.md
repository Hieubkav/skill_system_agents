# Real-time Streaming Patterns

## Basic Streaming Implementation
```typescript
// convex/streaming.ts
export const streamResponse = action({
  args: { 
    threadId: v.string(), 
    prompt: v.string() 
  },
  handler: async (ctx, { threadId, prompt }) => {
    const { thread } = await myAgent.continueThread(ctx, { threadId });

    // Stream with delta persistence
    await thread.streamText(
      { prompt },
      { 
        saveStreamDeltas: {
          chunking: "word",        // "word" | "line" | regex | function
          throttleMs: 200,         // Save every 200ms
        },
      }
    );

    return { streaming: true };
  },
});
```

## React Streaming Hook
```typescript
// frontend/StreamingChat.tsx
import { useUIMessages, useSmoothText } from "@convex-dev/agent/react";

export function StreamingChat({ threadId }: { threadId: string }) {
  const { results, status } = useUIMessages(
    api.streaming.listMessages,
    { threadId },
    { initialNumItems: 50, stream: true }
  );

  return (
    <div>
      {results?.map((message) => (
        <StreamingMessage key={message.key} message={message} />
      ))}
      <ChatInput 
        onSubmit={(prompt) => streamResponse({ threadId, prompt })}
        disabled={status === "loading"}
      />
    </div>
  );
}

function StreamingMessage({ message }: { message: UIMessage }) {
  // Smooth text animation for streaming messages
  const [visibleText] = useSmoothText(message.text, {
    startStreaming: message.status === "streaming",
  });

  return (
    <div className={`message ${message.role} ${message.status}`}>
      <div className="message-header">
        <strong>{message.agentName || message.role}</strong>
        {message.status === "streaming" && (
          <span className="typing-indicator">●●●</span>
        )}
      </div>
      <div className="message-content">
        {visibleText}
        {message.status === "streaming" && <span className="cursor">|</span>}
      </div>
    </div>
  );
}
```

## Advanced Streaming Configuration
```typescript
// Line-based chunking for structured content
export const streamCodeResponse = action({
  args: { threadId: v.string(), prompt: v.string() },
  handler: async (ctx, { threadId, prompt }) => {
    const { thread } = await myAgent.continueThread(ctx, { threadId });

    await thread.streamText(
      { prompt },
      {
        saveStreamDeltas: {
          chunking: "line",          // Better for code blocks
          throttleMs: 100,           // Faster for code
        },
      }
    );
  },
});

// Custom regex chunking
export const streamWithRegex = action({
  args: { threadId: v.string(), prompt: v.string() },
  handler: async (ctx, { threadId, prompt }) => {
    const { thread } = await myAgent.continueThread(ctx, { threadId });

    await thread.streamText(
      { prompt },
      {
        saveStreamDeltas: {
          chunking: /(?<=[.!?])\s+/,  // Chunk at sentence boundaries
          throttleMs: 300,
        },
      }
    );
  },
});
```

## Stream Delta Retrieval
```typescript
// Get messages with stream data included
export const listMessagesWithStreams = query({
  args: {
    threadId: v.string(),
    paginationOpts: paginationOptsValidator,
  },
  handler: async (ctx, { threadId, paginationOpts }) => {
    const messages = await listUIMessages(ctx, components.agent, {
      threadId,
      paginationOpts,
    });

    const streams = await syncStreams(ctx, components.agent, {
      threadId,
    });

    return { ...messages, streams };
  },
});
```

## Multi-Client Broadcasting
```typescript
// Broadcast streaming to multiple users
export const streamToMultiple = action({
  args: { 
    threadIds: v.array(v.string()), 
    prompt: v.string() 
  },
  handler: async (ctx, { threadIds, prompt }) => {
    const results = await Promise.allSettled(
      threadIds.map(async (threadId) => {
        const { thread } = await myAgent.continueThread(ctx, { threadId });
        return thread.streamText({ prompt }, { saveStreamDeltas: true });
      })
    );

    return { 
      successful: results.filter(r => r.status === "fulfilled").length,
      failed: results.filter(r => r.status === "rejected").length,
    };
  },
});
```

## Visual Streaming Indicators
```typescript
// Enhanced streaming UI with typing indicators
function EnhancedStreamingMessage({ message }: { message: UIMessage }) {
  const [visibleText] = useSmoothText(message.text, {
    startStreaming: message.status === "streaming",
  });

  return (
    <div className={`enhanced-message ${message.status}`}>
      {/* Avatar */}
      <div className="avatar">
        {message.role === "assistant" ? "🤖" : "👤"}
      </div>

      {/* Message content with streaming */}
      <div className="content">
        <div className="text">{visibleText}</div>
        
        {/* Streaming indicators */}
        {message.status === "streaming" && (
          <div className="streaming-indicators">
            <div className="typing-dots">
              <span></span><span></span><span></span>
            </div>
            <div className="progress-bar">
              <div className="progress" />
            </div>
          </div>
        )}
      </div>
    </div>
  );
}
```

## Error Handling in Streaming
```typescript
export const robustStreaming = action({
  args: { threadId: v.string(), prompt: v.string() },
  handler: async (ctx, { threadId, prompt }) => {
    const { thread } = await myAgent.continueThread(ctx, { threadId });

    try {
      await thread.streamText({ prompt });
      return { success: true };
    } catch (error) {
      // Mark stream as aborted
      await ctx.runMutation(internal.streaming.markStreamAborted, {
        threadId,
        error: error.message,
      });
      
      throw error;
    }
  },
});
```

## Performance Optimization
```typescript
// Optimized streaming for long responses
export const optimizedStreaming = action({
  args: { threadId: v.string(), prompt: v.string() },
  handler: async (ctx, { threadId, prompt }) => {
    const { thread } = await myAgent.continueThread(ctx, { threadId });

    await thread.streamText(
      { prompt },
      {
        saveStreamDeltas: {
          // Adaptive throttling based on content length
          chunking: (text) => {
            // Use smaller chunks for faster initial display
            if (text.length < 1000) return text.slice(0, 10);
            // Larger chunks for long content to reduce database writes
            return text.match(/.{1,50}/g)?.[0] || text.slice(0, 50);
          },
          throttleMs: (contentLength) => {
            // Faster initial updates, slower for long content
            return contentLength < 1000 ? 100 : 300;
          },
        },
      }
    );
  },
});
```

## Streaming Analytics
```typescript
// Track streaming performance
export const streamingAgent = new Agent(components.agent, {
  name: "Streaming Agent",
  languageModel: openai.chat("gpt-4o-mini"),
  
  // Track streaming metrics
  contextHandler: async (ctx, args) => {
    const start = Date.now();
    
    try {
      // Normal context handling
      return args.allMessages;
    } finally {
      // Log context generation time
      await ctx.runMutation(internal.analytics.trackContextTime, {
        duration: Date.now() - start,
        contextSize: args.allMessages.length,
      });
    }
  },
});

export const analyticalStreaming = action({
  args: { threadId: v.string(), prompt: v.string() },
  handler: async (ctx, { threadId, prompt }) => {
    const startTime = Date.now();
    const { thread } = await analyticalAgent.continueThread(ctx, { threadId });

    await thread.streamText({ prompt });

    return { 
      startTime,
      firstChunkTime: await ctx.runQuery(
        internal.streaming.getFirstChunkTime,
        { threadId }
      ),
    };
  },
});
```

## Streaming Best Practices
```typescript
// 1. Use word chunking for natural chat interaction
// 2. Use line chunking for code or structured content
// 3. Set appropriate throttleMs (200-500ms typical)
// 4. Handle stream errors gracefully
// 5. Provide visual feedback during streaming
// 6. Implement stream persistence for reload scenarios
// 7. Monitor stream performance and optimize
// 8. Use progressive enhancement for fallback

// Resource cleanup
export const cleanupOldStreams = action({
  args: {},
  handler: async (ctx) => {
    const cutoff = Date.now() - (24 * 60 * 60 * 1000); // 24 hours
    
    await ctx.runMutation(internal.streaming.cleanupStreams, {
      cutoffTimestamp: cutoff,
    });
    
    return { cleaned: true };
  },
});
```

## Streaming + Tools Combination
```typescript
export const streamWithTools = action({
  args: { threadId: v.string(), prompt: v.string() },
  handler: async (ctx, { threadId, prompt }) => {
    const { thread } = await myAgent.continueThread(ctx, { threadId });

    await thread.streamText(
      { prompt },
      {
        tools: {
          weather: weatherTool,
          search: searchTool,
        },
        maxSteps: 5,
        saveStreamDeltas: {
          chunking: "word",
          throttleMs: 200,
        },
      }
    );
  },
});
```
