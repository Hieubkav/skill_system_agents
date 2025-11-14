---
name: convex-agents
description: Xử lý Convex Agents functionality: AI agents setup, context management, messages, human-in-the-loop workflows, file processing, RAG, streaming, debugging, tools, rate limiting, usage tracking, playground testing, thread management, agent orchestration. Dùng khi xây dựng AI chat agents, automation workflows, AI-powered applications.
---

# Convex Agents Skill

## Level 1: Overview

Hướng dẫn toàn diện build AI agents với Convex agents system. Gộp functionality từ 14 agent skills thành comprehensive solution.

### Skill này làm gì?
- Xử lý AI agent development và orchestration
- Build chat interfaces với context management
- Manage human-in-the-loop workflows và file processing
- Implement RAG, streaming, tool calling capabilities

### Yêu cầu gì?
- Convex project với agents component setup
- Hiểu AI/LLM concepts và chat interfaces  
- Biết TypeScript và React basics

### Output ra sao?
- Complete AI agent implementations
- Chat interfaces dengan real-time capabilities
- Workflow automation và human collaboration patterns

## Level 2: Quick Start

### Các bước cơ bản

```typescript
// 1. Setup agent básico
import { Agent } from "@convex-dev/agent";
import { openai } from "@ai-sdk/openai";

export const myAgent = new Agent(components.agent, {
  name: "My Assistant",
  languageModel: openai.chat("gpt-4o-mini"),
  instructions: "You are a helpful assistant.",
});

// 2. Create thread
export const createThread = action({
  args: { userId: v.string() },
  handler: async (ctx, { userId }) => {
    const { thread } = await myAgent.createThread(ctx, { userId });
    return { threadId: thread.getMetadata().threadId };
  },
});

// 3. Generate response
export const chat = action({
  args: { threadId: v.string(), prompt: v.string() },
  handler: async (ctx, { threadId, prompt }) => {
    const { thread } = await myAgent.continueThread(ctx, { threadId });
    const result = await thread.streamText({ prompt });
    return result;
  },
});
```

### React streaming interface
```typescript
import { useUIMessages } from "@convex-dev/agent/react";

function ChatInterface({ threadId }: { threadId: string }) {
  const { results } = useUIMessages(
    api.agents.listMessages,
    { threadId },
    { stream: true }
  );

  return (
    <div>
      {results?.map((msg) => (
        <MessageComponent key={msg.key} message={msg} />
      ))}
    </div>
  );
}
```

### Best practices
- Thread isolation cho setiap conversation
- Context optimization để prevent token overflow
- Human-in-the-loop escalation patterns
- Real-time streaming cho optimal UX
- Rate limiting và usage tracking

## Level 3: References

### Core agent functionality
- [agent-fundamentals](references/agent-fundamentals.md) - Agent setup, threads, basic generation
- [context-management](references/context.md) - Context handlers, RAG integration, vector search
- [message-handling](references/messages.md) - Message persistence, React hooks, streaming
- [tool-calling](references/tools.md) - Tool definition, validation, execution patterns

### Advanced workflows
- [human-in-the-loop](references/human-agents.md) - Escalation, approval workflows, hybrid patterns
- [file-processing](references/files.md) - Upload workflows, image analysis, media handling
- [streaming-realtime](references/streaming.md) - Character streaming, delta persistence, multi-client
- [rate-limiting](references/usage.md) - Token tracking, quota management, cost optimization

### Development & testing
- [debugging-tools](references/debugging.md) - Request response logging, context inspection, playground
- [workflow-orchestration](references/workflows.md) - Multi-step processes, state management, error recovery
- [playground-testing](references/playground.md) - Interactive testing, prompt iteration, sandbox workflow
- [rag-implementation](references/rag.md) - Knowledge base integration, search patterns, context injection
