# Timestamps  (`day-separator`)

Chronology in a long thread: days marked, times on hover.

- 完整文件：`references/pages/day-separator.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-day-separator.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-day-separator.md`

## With a runtime

Every message carries a real `createdAt` (a `Date`) and `role`, and a thread's full message list is available as plain state, so building the day-grouped shape is formatting and text extraction, not new wiring.

```tsx
"use client";

import { useAuiState } from "@assistant-ui/react";
import { DaySeparator, type DatedMessage } from "@/components/assistant-ui/elements/day-separator";

const dayFormat = new Intl.DateTimeFormat("en-US", { month: "short", day: "numeric" });
const timeFormat = new Intl.DateTimeFormat("en-US", { hour: "numeric", minute: "2-digit" });

export function ThreadHistory() {
  const messages = useAuiState((s) => s.thread.messages);

  const dated: DatedMessage[] = messages
    .filter((m) => m.role !== "system")
    .map((m) => ({
      id: m.id,
      day: dayFormat.format(m.createdAt),
      time: timeFormat.format(m.createdAt),
      role: m.role === "user" ? "user" : "assistant",
      text: m.content
        .filter((part): part is { type: "text"; text: string } => part.type === "text")
        .map((part) => part.text)
        .join(" "),
    }));

  return <DaySeparator messages={dated} />;
}

```

## Standalone (no runtime)

Standalone, DaySeparator takes the whole list at once: you hold the array and append to it as the conversation grows.

```tsx
"use client";

import { useState } from "react";
import { DaySeparator, type DatedMessage } from "@/components/assistant-ui/elements/day-separator";

const MESSAGES: DatedMessage[] = [
  { id: "1", day: "Yesterday", time: "16:04", role: "user", text: "Why does the draft survive a reload?" },
  { id: "2", day: "Yesterday", time: "16:04", role: "assistant", text: "It's persisted per thread." },
];

export function History() {
  const [messages, setMessages] = useState(MESSAGES);
  return <DaySeparator messages={messages} />;
}

```
