# Edit a sent message  (`edit-message`)

Rewrite a turn in place as a new branch, told up front how many replies stay behind on the old one.

- 完整文件：`references/pages/edit-message.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-edit-message.md`
- Thread 內建：`Thread` already ships this exact swap for user and assistant messages, without the branch notice covered below; install `@assistant-ui/thread` for the full composition.

## With a runtime

`s.message.composer.isEditing` flips true the moment `ActionBarPrimitive.Edit` is pressed; render an edit composer in place of the bubble while it's true.

```tsx
"use client";

import { ActionBarPrimitive, MessagePrimitive, useAuiState } from "@assistant-ui/react";
import { PencilLineIcon } from "lucide-react";
import { cn } from "@/lib/utils";
import { field, ghostButton } from "@/components/assistant-ui/elements/surfaces";

export function UserTurn() {
  const isEditing = useAuiState((s) => s.message.composer.isEditing);

  return (
    <MessagePrimitive.Root className="flex w-full max-w-sm flex-col items-end gap-1">
      {isEditing ? (
        <EditComposer />
      ) : (
        <div className={cn(field, "flex items-center gap-2 rounded-2xl px-3.5 py-2.5")}>
          <MessagePrimitive.Parts />
          <ActionBarPrimitive.Edit aria-label="Edit message" className={cn(ghostButton, "size-6 shrink-0")}>
            <PencilLineIcon className="size-3.5" />
          </ActionBarPrimitive.Edit>
        </div>
      )}
    </MessagePrimitive.Root>
  );
}

```

## Standalone (no runtime)

Standalone, `EditMessage` is a controlled component: the bubble calls `onStartEdit`, and while `editing` is true it renders a textarea plus Cancel and Send.

```tsx
"use client";

import { useState } from "react";
import { EditMessage } from "@/components/assistant-ui/elements/edit-message";

export function Turn() {
  const [editing, setEditing] = useState(false);
  const [value, setValue] = useState("What's the capital of France?");

  return (
    <EditMessage
      value={value}
      discardedReplies={2}
      editing={editing}
      onValueChange={setValue}
      onStartEdit={() => setEditing(true)}
      onCancel={() => setEditing(false)}
      onSave={async () => {
        await resend(value);
        setEditing(false);
      }}
    />
  );
}

```
