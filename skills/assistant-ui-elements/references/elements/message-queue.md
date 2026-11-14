# Message queue  (`message-queue`)

Turns you typed while a run was in flight, stacked and cancelable until it finishes.

- 完整文件：`references/pages/message-queue.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-message-queue.md`

## With a runtime

A runtime that supports queueing tracks pending sends on `s.composer.queue`; nothing special is required to add to it besides sending while `s.thread.isRunning` is already true.

```tsx
"use client";

import { ComposerPrimitive, QueueItemPrimitive, useAuiState } from "@assistant-ui/react";
import { ArrowUpIcon, XIcon } from "lucide-react";
import { cn } from "@/lib/utils";
import { field, ghostButton, mono } from "@/components/assistant-ui/elements/surfaces";

export function MessageQueue() {
  const queueLength = useAuiState((s) => s.composer.queue.length);

  return (
    <div className="flex w-full max-w-sm flex-col gap-2">
      {queueLength > 0 && (
        <div className="flex items-baseline justify-between px-1">
          <span className={cn(mono, "text-muted-foreground")}>{queueLength} queued</span>
          <span className={cn(mono, "text-muted-foreground")}>sends when this finishes</span>
        </div>
      )}
      <ul className="flex flex-col gap-1.5">
        <ComposerPrimitive.Queue>
          {({ queueItem }) => (
            <li
              key={queueItem.id}
              className={cn(field, "flex items-center gap-2.5 rounded-2xl py-2 pr-2 pl-3")}
            >
              <span className="text-foreground/60 min-w-0 flex-1 truncate text-[13.5px]">
                <QueueItemPrimitive.Text />
              </span>
              <ArrowUpIcon className="text-foreground/25 size-3 shrink-0" />
              <QueueItemPrimitive.Remove aria-label="Remove from queue" className={cn(ghostButton, "size-6 shrink-0")}>
                <XIcon className="size-3.5" />
              </QueueItemPrimitive.Remove>
            </li>
          )}
        </ComposerPrimitive.Queue>
      </ul>
    </div>
  );
}

```

## Standalone (no runtime)

Standalone, `MessageQueue` renders what you hand it: the running message's text, and the array of queued messages behind it.

```tsx
"use client";

import { useState } from "react";
import { MessageQueue, type QueuedMessage } from "@/components/assistant-ui/elements/message-queue";

export function Queue() {
  const [queued, setQueued] = useState<QueuedMessage[]>([
    { id: "1", text: "Now add tests for the auth flow" },
  ]);

  return (
    <MessageQueue
      running="Refactor the login form to use the new validation hook"
      queued={queued}
      onCancel={(id) => setQueued((q) => q.filter((m) => m.id !== id))}
    />
  );
}

```
