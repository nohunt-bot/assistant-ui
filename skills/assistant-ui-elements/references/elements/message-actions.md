# Message actions  (`message-actions`)

Copy, rate, and regenerate. Each action confirms itself with a small state change.

- 完整文件：`references/pages/message-actions.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-message-actions.md`

## With a runtime

`ActionBarPrimitive` supplies one button per action, each already wired to the message it renders inside and disabled the moment that action stops being available.

```tsx
"use client";

import { ActionBarPrimitive, AuiIf } from "@assistant-ui/react";
import {
  CheckIcon,
  CopyIcon,
  RefreshCwIcon,
  ThumbsDownIcon,
  ThumbsUpIcon,
} from "lucide-react";
import { cn } from "@/lib/utils";
import { ghostButton } from "@/components/assistant-ui/elements/surfaces";

export function MessageActions() {
  const buttonClassName = cn(ghostButton, "size-7");

  return (
    <ActionBarPrimitive.Root className="flex items-center gap-1">
      <ActionBarPrimitive.Copy aria-label="Copy response" className={buttonClassName}>
        <AuiIf condition={(s) => s.message.isCopied}>
          <CheckIcon className="size-3.5" />
        </AuiIf>
        <AuiIf condition={(s) => !s.message.isCopied}>
          <CopyIcon className="size-3.5" />
        </AuiIf>
      </ActionBarPrimitive.Copy>
      <ActionBarPrimitive.FeedbackPositive aria-label="Mark response helpful" className={buttonClassName}>
        <ThumbsUpIcon className="size-3.5" />
      </ActionBarPrimitive.FeedbackPositive>
      <ActionBarPrimitive.FeedbackNegative aria-label="Mark response unhelpful" className={buttonClassName}>
        <ThumbsDownIcon className="size-3.5" />
      </ActionBarPrimitive.FeedbackNegative>
      <ActionBarPrimitive.Reload aria-label="Regenerate response" className={buttonClassName}>
        <RefreshCwIcon className="size-3.5" />
      </ActionBarPrimitive.Reload>
    </ActionBarPrimitive.Root>
  );
}

```

## Standalone (no runtime)

Standalone, `MessageActions` is fully controlled: five callbacks and the pieces of state that decide how each button currently looks.

```tsx
npx shadcn@latest add "@assistant-ui/use-copy-to-clipboard"

```
