# Memory  (`memory-chips`)

What it now remembers about you, written during the turn and removable.

- 完整文件：`references/pages/memory-chips.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-memory-chips.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-memory-chips.md`

## With a runtime

assistant-ui has no built-in memory concept: nothing on a message tracks facts learned about the user. The closest real building block is a tool call, the same way a graded claim or a comparison table would be. Give the model a `remember` tool and collect every call it makes in the current message into the chip list.

```tsx
"use client";

import { useAuiState } from "@assistant-ui/react";
import type { ToolCallMessagePart } from "@assistant-ui/react";
import {
  MemoryChips,
  type MemoryChip,
} from "@/components/assistant-ui/elements/memory-chips";

type RememberCall = ToolCallMessagePart<
  { text: string; change: "added" | "updated" },
  void
>;

function MemoryFromMessage() {
  const chips = useAuiState((s): MemoryChip[] =>
    s.message.role === "assistant"
      ? s.message.content
          .filter(
            (part): part is RememberCall =>
              part.type === "tool-call" && part.toolName === "remember",
          )
          .map((part) => ({
            id: part.toolCallId,
            text: part.args.text,
            change: part.args.change,
          }))
      : [],
  );

  if (chips.length === 0) return null;
  return <MemoryChips chips={chips} onForget={forgetMemory} />;
}

```

## Standalone (no runtime)

Standalone, you hold the whole chip list, including whatever it remembered before this turn.

```tsx
"use client";

import { useState } from "react";
import {
  MemoryChips,
  type MemoryChip,
} from "@/components/assistant-ui/elements/memory-chips";

const INITIAL: MemoryChip[] = [
  { id: "1", text: "Prefers TypeScript", change: "existing" },
  { id: "2", text: "Works on assistant-ui", change: "existing" },
];

export function Memory() {
  const [chips, setChips] = useState(INITIAL);
  return (
    <MemoryChips
      chips={chips}
      onForget={(id) => setChips((c) => c.filter((chip) => chip.id !== id))}
    />
  );
}

```
