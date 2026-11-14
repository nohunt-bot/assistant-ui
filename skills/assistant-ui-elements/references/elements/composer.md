# Composer  (`composer`)

The unified input: attachments, commands, mentions, models, voice, and context in one surface.

- 完整文件：`references/pages/composer.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-composer.md`

## With a runtime

Every assistant-ui runtime carries a composer scoped to the active thread (or to a message being edited): a place to hold the draft text, staged attachments, and the send and cancel actions. Build the bar from `ComposerPrimitive`, the same primitives the `Thread` element renders internally, and pair them with the shared surface tokens from `surfaces.tsx` to match this catalog's look.

```tsx
"use client";

import { AuiIf, ComposerPrimitive } from "@assistant-ui/react";
import { ArrowUpIcon, PlusIcon, SquareIcon } from "lucide-react";
import { cn } from "@/lib/utils";
import { ghostButton, inkButton, paper } from "@/components/assistant-ui/elements/surfaces";

export function ComposerBar() {
  return (
    <ComposerPrimitive.Root className={cn(paper, "flex w-full max-w-lg flex-col gap-2 rounded-[24px] p-2.5")}>
      <ComposerPrimitive.Input
        placeholder="Message..."
        rows={1}
        className="placeholder:text-muted-foreground min-h-11 w-full resize-none bg-transparent px-3 text-[15px] outline-none"
      />
      <div className="flex items-center justify-between">
        <ComposerPrimitive.AddAttachment
          aria-label="Add attachment"
          className={cn(ghostButton, "size-8 disabled:pointer-events-none disabled:opacity-30")}
        >
          <PlusIcon className="size-4" />
        </ComposerPrimitive.AddAttachment>
        <AuiIf condition={(s) => !s.thread.isRunning}>
          <ComposerPrimitive.Send
            aria-label="Send message"
            className={cn(inkButton, "grid size-8 place-items-center rounded-full")}
          >
            <ArrowUpIcon className="size-4" />
          </ComposerPrimitive.Send>
        </AuiIf>
        <AuiIf condition={(s) => s.thread.isRunning}>
          <ComposerPrimitive.Cancel
            aria-label="Stop generating"
            className={cn(inkButton, "grid size-8 place-items-center rounded-full")}
          >
            <SquareIcon className="size-3 fill-current" />
          </ComposerPrimitive.Cancel>
        </AuiIf>
      </div>
    </ComposerPrimitive.Root>
  );
}

```

## Standalone (no runtime)

Standalone, `Composer` and its children are presentational: they render the shape and animate the visible states, but hold no text, attachments, or run status of their own. You own that state and pass it down as props and event handlers.

```tsx
"use client";

import { useState } from "react";
import { Composer, ComposerBar, ComposerInput, ComposerToolbar, ComposerActions, ComposerAttachButton, ComposerSend } from "@/components/assistant-ui/elements/composer";

export function ChatBox() {
  const [text, setText] = useState("");
  const [sending, setSending] = useState(false);

  const send = () => {
    if (!text.trim()) return;
    setSending(true);
    submitMessage(text).finally(() => setSending(false));
    setText("");
  };

  return (
    <Composer>
      <ComposerBar>
        <ComposerInput
          value={text}
          onChange={(e) => setText(e.target.value)}
          onSubmit={send}
          placeholder="Message..."
        />
        <ComposerToolbar>
          <ComposerAttachButton onClick={() => addAttachment()} />
          <ComposerActions>
            <ComposerSend streaming={sending} idle={text.trim().length === 0} onClick={send} />
          </ComposerActions>
        </ComposerToolbar>
      </ComposerBar>
    </Composer>
  );
}

```
