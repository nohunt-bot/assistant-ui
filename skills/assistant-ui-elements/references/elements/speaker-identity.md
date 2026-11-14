# Speaker identity  (`speaker-identity`)

Who is talking, once a thread holds more than a user and one model.

- 完整文件：`references/pages/speaker-identity.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-speaker-identity.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-speaker-identity.md`

## With a runtime

`s.message.role` gives you the user/assistant split directly. Tool calls live as parts inside an assistant message's `content`, each with a `toolName` and, when the runtime tracks it, a `timing`. A nested subagent run shows up as a tool call's own `messages` field, a real part of `ToolCallMessagePart` for exactly this case. assistant-ui has no fixed "agent name" concept beyond that: a display name for a particular agent or model is something you attach yourself through the message's open `metadata.custom` bag.

```tsx
"use client";

import type { ThreadMessage } from "@assistant-ui/react";
import type { SpeakerTurn } from "@/components/assistant-ui/elements/speaker-identity";

function textOf(content: readonly { type: string; text?: string }[]) {
  return content
    .filter((part): part is { type: "text"; text: string } => part.type === "text")
    .map((part) => part.text)
    .join(" ");
}

function customString(message: ThreadMessage, key: string): string | undefined {
  const value = message.metadata.custom[key];
  return typeof value === "string" ? value : undefined;
}

function messageTurn(message: ThreadMessage): SpeakerTurn {
  if (message.role === "user") {
    return { id: message.id, kind: "user", name: "You", text: textOf(message.content) };
  }
  return {
    id: message.id,
    kind: "agent",
    name: customString(message, "agentName") ?? "Assistant",
    detail: customString(message, "model"),
    text: textOf(message.content),
  };
}

```

## Standalone (no runtime)

Standalone, SpeakerIdentity takes a flat list of turns; you decide what counts as a turn and how they're ordered.

```tsx
import { SpeakerIdentity, type SpeakerTurn } from "@/components/assistant-ui/elements/speaker-identity";

const TURNS: SpeakerTurn[] = [
  { id: "1", kind: "user", name: "You", text: "Find out why the converter drops turns." },
  { id: "2", kind: "agent", name: "Maintainer", detail: "opus", text: "Splitting this up." },
  { id: "3", kind: "subagent", name: "reader", detail: "haiku", text: "convertMessages returns early." },
];

<SpeakerIdentity turns={TURNS} />;

```
