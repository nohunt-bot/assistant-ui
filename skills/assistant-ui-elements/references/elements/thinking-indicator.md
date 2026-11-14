# Thinking indicator  (`thinking-indicator`)

A live status line that names what the agent is doing right now, with elapsed time.

- 完整文件：`references/pages/thinking-indicator.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-thinking-indicator.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-thinking-indicator.md`

## With a runtime

The runtime does not hand you a phrase like "Reading the docs"; it hands you the parts that make up the message so far. The most concrete label you can build from that is the name of whatever tool call is still pending, falling back to a plain "Thinking" while the run is active and nothing else is happening yet.

```tsx
"use client";

import { useAuiState } from "@assistant-ui/react";

function useThinkingLabel() {
  return useAuiState((s) => {
    if (s.message.status?.type !== "running") return undefined;
    const pending = s.message.parts.find(
      (part) => part.type === "tool-call" && part.result === undefined,
    );
    if (pending?.type === "tool-call") return `Running ${pending.toolName}`;
    return s.message.parts.length === 0 ? "Thinking" : undefined;
  });
}

```

## Standalone (no runtime)

Standalone, `label` is a plain string and `elapsed` is a plain string; the component only handles the fade between labels and the layout.

```tsx
"use client";

import { useState } from "react";
import { ThinkingIndicator } from "@/components/assistant-ui/elements/thinking-indicator";

export function Status() {
  const [label, setLabel] = useState("Thinking");
  return <ThinkingIndicator label={label} />;
}

```
