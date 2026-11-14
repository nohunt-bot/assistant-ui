# Scroll anchor  (`scroll-anchor`)

Streaming never steals your scroll position; a pill offers the way back down.

- 完整文件：`references/pages/scroll-anchor.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-scroll-anchor.md`

## With a runtime

import { ThreadPrimitive } from "@assistant-ui/react";

```tsx
"use client";

import { ThreadPrimitive } from "@assistant-ui/react";

function Viewport() {
  return (
    <ThreadPrimitive.Viewport className="relative h-64 w-full max-w-sm overflow-hidden rounded-2xl">
      <ThreadPrimitive.Messages>{({ message }) => null}</ThreadPrimitive.Messages>
    </ThreadPrimitive.Viewport>
  );
}

```

## Standalone (no runtime)

Standalone, `ScrollAnchor` owns the whole simulation: it appends `messages` on a timer, decides when it's pinned, and shows the pill itself.

```tsx
"use client";

import { useState } from "react";
import { ScrollAnchor, type ScrollAnchorMessage } from "@/components/assistant-ui/elements/scroll-anchor";

const messages: ScrollAnchorMessage[] = [
  { role: "user", text: "Keep summarizing as you go." },
  { role: "assistant", text: "Starting now." },
  { role: "assistant", text: "Here's the first section..." },
];

export function ScrollDemo() {
  const [paused, setPaused] = useState(false);
  return <ScrollAnchor messages={messages} paused={paused} onSettled={() => setPaused(true)} />;
}

```
