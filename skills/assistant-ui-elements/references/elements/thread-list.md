# Thread list  (`thread-list`)

Runtime-backed conversation switching with search, active selection, and thread actions.

- 完整文件：`references/pages/thread-list.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/thread-list.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-thread-list.md`

## With a runtime

`ThreadList` takes no props: mount it anywhere inside a runtime provider and it reads and writes the thread list on its own.

```tsx
import { ThreadList } from "@/components/assistant-ui/elements/thread-list.aui";

export function Sidebar() {
  return (
    <aside className="w-64 border-e p-2">
      <ThreadList />
    </aside>
  );
}

```

## Standalone (no runtime)

Standalone, the element is a controlled row list: you hold the threads and the active index, it renders rows, and it reports selection, rename, and delete clicks. There is no built-in new-thread button or search; both stay outside the element.

```tsx
"use client";

import { useState } from "react";
import {
  ThreadList,
  type ThreadItem,
} from "@/components/assistant-ui/elements/thread-list";

const threads: ThreadItem[] = [
  { id: "rename", title: "Renaming a component", time: "2m", unread: true },
  { id: "deploy", title: "Deploy checklist", time: "1h" },
];

export function Sidebar() {
  const [activeIndex, setActiveIndex] = useState(0);
  return (
    <ThreadList
      threads={threads}
      activeIndex={activeIndex}
      onActiveIndexChange={setActiveIndex}
    />
  );
}

```
