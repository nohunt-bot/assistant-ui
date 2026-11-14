# Typing indicator  (`typing-indicator`)

The classic three dots, tuned to read as presence rather than noise.

- 完整文件：`references/pages/typing-indicator.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-typing-indicator.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-typing-indicator.md`

## With a runtime

This belongs on screen for exactly one window: a run is active and the newest message has not produced any content yet. Nothing about the dots themselves depends on the runtime; only the decision to render it does.

```tsx
"use client";

import { useAuiState } from "@assistant-ui/react";
import { TypingIndicator } from "@/components/assistant-ui/elements/typing-indicator";

function AssistantTyping() {
  const waiting = useAuiState((s) => {
    if (!s.thread.isRunning) return false;
    const last = s.thread.messages.at(-1);
    return last?.role === "assistant" && last.parts.length === 0;
  });
  if (!waiting) return null;
  return <TypingIndicator />;
}

```

## Standalone (no runtime)

Standalone, `TypingIndicator` takes no data at all; mounting and unmounting it is entirely up to you.

```tsx
"use client";

import { useState } from "react";
import { TypingIndicator } from "@/components/assistant-ui/elements/typing-indicator";

export function Chat() {
  const [waiting, setWaiting] = useState(false);
  return waiting ? <TypingIndicator /> : null;
}

```
