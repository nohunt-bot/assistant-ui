# Trace waterfall  (`trace-waterfall`)

Every span in a run on one time axis, nested, so you can see where it actually went.

- 完整文件：`references/pages/trace-waterfall.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-trace-waterfall.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-trace-waterfall.md`

## With a runtime

assistant-ui doesn't model nested spans itself. A message carries its own stream timing, and each tool call and reasoning part can carry its own start and completion time, but nothing ties them into a tree deeper than "the message" and "the parts inside it." That's enough for a flat, two-level waterfall.

```tsx
"use client";

import { useAuiState, useMessageTiming } from "@assistant-ui/react";
import {
  TraceWaterfall,
  type TraceSpan,
} from "@/components/assistant-ui/elements/trace-waterfall";

function useMessageTrace(): { spans: TraceSpan[]; totalMs: number } {
  const timing = useMessageTiming();
  const parts = useAuiState((s) => s.message.parts);
  const totalMs = timing?.totalStreamTime ?? 0;

  const spans: TraceSpan[] = [
    {
      id: "message",
      name: "message",
      depth: 0,
      startMs: 0,
      durationMs: totalMs,
      status: totalMs > 0 ? "completed" : "running",
    },
  ];

  for (const [index, part] of parts.entries()) {
    if ((part.type !== "tool-call" && part.type !== "reasoning") || !part.timing)
      continue;
    const streamStart = timing?.streamStartTime ?? part.timing.startedAt;
    spans.push({
      id:
        part.type === "tool-call"
          ? `tool-${part.toolCallId}`
          : `reasoning-${index}`,
      name: part.type === "tool-call" ? part.toolName : "reasoning",
      depth: 1,
      startMs: Math.max(0, part.timing.startedAt - streamStart),
      durationMs: part.timing.completedAt
        ? part.timing.completedAt - part.timing.startedAt
        : 0,
      status:
        part.status.type === "running"
          ? "running"
          : part.status.type === "complete"
            ? "completed"
            : "failed",
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, you own the full span list and the total, and grow both as a run proceeds.

```tsx
"use client";

import { useState } from "react";
import {
  TraceWaterfall,
  type TraceSpan,
} from "@/components/assistant-ui/elements/trace-waterfall";

const SPANS: TraceSpan[] = [
  { id: "run", name: "run", depth: 0, startMs: 0, durationMs: 1840, status: "completed" },
  { id: "model", name: "chat.completions", depth: 1, startMs: 40, durationMs: 720, status: "completed" },
  { id: "search", name: "web_search", depth: 1, startMs: 790, durationMs: 460, status: "completed" },
];

export function Run() {
  const [visibleCount, setVisibleCount] = useState(0);
  return <TraceWaterfall spans={SPANS} totalMs={1840} visibleCount={visibleCount} />;
}

```
