# Task card  (`task-card`)

A delegated task with its state, timing, result, and transcript in one card.

- 完整文件：`references/pages/task-card.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/task-card.md`、`source/<flavor>/aui/thread.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-task-card.md`

## With a runtime

import { TaskGroup } from "@/components/assistant-ui/elements/task-card.aui";
   import { Thread, type ThreadComponents } from "@/components/assistant-ui/elements/thread.aui";

```tsx
"use client";

import { TaskGroup } from "@/components/assistant-ui/elements/task-card.aui";
import { Thread, type ThreadComponents } from "@/components/assistant-ui/elements/thread.aui";

const THREAD_COMPONENTS: ThreadComponents = { TaskGroup };

export function Conversation() {
  return <Thread components={THREAD_COMPONENTS} />;
}

```

## Standalone (no runtime)

Standalone, `TaskCard` is props only. Give it a label, optional meta tag, state (`"working"`, `"waiting"`, `"done"`, `"failed"`, or `"cancelled"`), optional elapsed time, actions, and result, plus open state and transcript children.

```tsx
"use client";

import { useState } from "react";
import { TaskCard } from "@/components/assistant-ui/elements/task-card";

export function Task() {
  const [open, setOpen] = useState(false);

  return (
    <TaskCard
      label="Review the runtime"
      meta="research"
      state="done"
      elapsed="4.2s"
      result="Found the relevant runtime path."
      open={open}
      onOpenChange={setOpen}
    >
      <p>Read the runtime entry point.</p>
      <p>Checked the thread lifecycle.</p>
    </TaskCard>
  );
}

```
