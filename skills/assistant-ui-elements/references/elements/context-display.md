# Context display  (`context-display`)

Model context usage as a ring, bar, or text value with a detailed hover view.

- 完整文件：`references/pages/context-display.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/context-display.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-context-display.md`

## With a runtime

Runtime usage needs two things: your server returning token counts, and a preset mounted somewhere in the UI.

```tsx
import { streamText, convertToModelMessages } from "ai";

export async function POST(req: Request) {
  const { messages } = await req.json();
  const result = streamText({
    abortSignal: req.signal,
    model: getModel(),
    messages: await convertToModelMessages(messages),
  });
  return result.toUIMessageStreamResponse({
    messageMetadata: ({ part }) => {
      if (part.type === "finish") return { usage: part.totalUsage };
      if (part.type === "finish-step")
        return { modelId: part.response.modelId };
      return undefined;
    },
  });
}

```

## Standalone (no runtime)

Standalone, every preset and the composable `Root` take a `usage` object as a prop instead of reading one from a runtime, plus an optional `resetKey` to restart the running total yourself (a thread id, for instance) when you switch conversations.

```tsx
import { ContextDisplayBar } from "@/components/assistant-ui/elements/context-display";

export function UsageBadge() {
  return (
    <ContextDisplayBar
      modelContextWindow={128000}
      usage={{ totalTokens: 42000, inputTokens: 38000, outputTokens: 4000 }}
    />
  );
}

```
