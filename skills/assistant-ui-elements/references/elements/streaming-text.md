# Streaming text  (`streaming-text`)

Tokens arrive softly: the newest words land in blue and settle into ink.

- 完整文件：`references/pages/streaming-text.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-streaming-text.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-streaming-text.md`

## With a runtime

`MessagePrimitive.Parts` hands a custom part renderer the part's own fields as props, `text` and `status` included, so there is no selector to write: `count` is the number of words in that string.

```tsx
"use client";

import { useMemo } from "react";
import type { TextMessagePartComponent } from "@assistant-ui/react";
import { StreamingText, type Segment } from "@/components/assistant-ui/elements/streaming-text";

const StreamingTextPart: TextMessagePartComponent = ({ text, status }) => {
  const segments = useMemo<Segment[]>(() => [{ text }], [text]);
  const count = useMemo(() => text.split(" ").length, [text]);
  return <StreamingText segments={segments} count={count} streaming={status.type === "running"} />;
};

```

## Standalone (no runtime)

Standalone, you decide both what the segments say and how many words are currently revealed; the element only handles the fade and the coloring.

```tsx
"use client";

import { useState } from "react";
import { StreamingText, type Segment } from "@/components/assistant-ui/elements/streaming-text";

const SEGMENTS: Segment[] = [
  { text: "The response streams in as" },
  { text: "useAuiState", mono: true },
  { text: "resolves each part." },
];

export function Answer() {
  const [count, setCount] = useState(0);
  return <StreamingText segments={SEGMENTS} count={count} streaming={count < 12} />;
}

```
