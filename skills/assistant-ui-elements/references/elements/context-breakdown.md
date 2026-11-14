# Context breakdown  (`context-breakdown`)

Where the window actually went: prompt, tools, files, conversation, and what's left.

- 完整文件：`references/pages/context-breakdown.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-context-breakdown.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-context-breakdown.md`

## With a runtime

With a runtime, assistant-ui does not label context usage by category the way this element does. What a `ChatModelAdapter` can report is two real numbers per turn: input tokens and output tokens, carried on each assistant message's generation steps.

```tsx
"use client";

import { useMemo } from "react";
import { useAuiState } from "@assistant-ui/react";
import {
  ContextBreakdown,
  type ContextSegment,
} from "@/components/assistant-ui/elements/context-breakdown";

function useTurnUsage() {
  const messages = useAuiState((s) => s.thread.messages);
  return useMemo(() =>
    messages.reduce(
      (sum, message) => {
        for (const step of message.metadata.steps ?? []) {
          sum.input += step.usage?.inputTokens ?? 0;
          sum.output += step.usage?.outputTokens ?? 0;
        }
        return sum;
      },
      { input: 0, output: 0 },
    ),
    [messages],
  );
}

const MODEL_CONTEXT_WINDOW = 128_000;

export function ThreadContextBreakdown() {
  const usage = useTurnUsage();
  const segments: ContextSegment[] = [
    { label: "Prompt", tokens: usage.input, tint: "bg-foreground/30" },
    { label: "Reply", tokens: usage.output, tint: "bg-blue-500/70" },
  ];
  return <ContextBreakdown segments={segments} limit={MODEL_CONTEXT_WINDOW} />;
}

```

## Standalone (no runtime)

Standalone, `ContextBreakdown` renders whatever segments you pass; it only sums their tokens against `limit` to compute the bar widths and the leftover headroom.

```tsx
"use client";

import { ContextBreakdown } from "@/components/assistant-ui/elements/context-breakdown";

const segments = [
  { label: "System prompt", tokens: 1_400, tint: "bg-foreground/20" },
  { label: "Tools", tokens: 3_100, tint: "bg-foreground/35" },
  { label: "Conversation", tokens: 42_000, tint: "bg-blue-500/70" },
];

export function ContextPanel() {
  return <ContextBreakdown segments={segments} limit={128_000} />;
}

```
