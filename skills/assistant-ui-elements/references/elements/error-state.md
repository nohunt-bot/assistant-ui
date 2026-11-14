# Error state  (`error-state`)

A quiet failure banner with a retry path, not a modal in your face.

- 完整文件：`references/pages/error-state.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-error-state.md`

## With a runtime

An assistant message that failed mid-run carries its error on `s.message.status`; `MessagePrimitive.Error` only renders its children while one is present, so you never need to check for it yourself.

```tsx
"use client";

import { ErrorPrimitive, MessagePrimitive } from "@assistant-ui/react";
import { CircleAlertIcon } from "lucide-react";

export function MessageError() {
  return (
    <MessagePrimitive.Error>
      <ErrorPrimitive.Root className="flex items-start gap-2.5 rounded-2xl bg-red-500/[0.06] px-4 py-3 text-sm dark:bg-red-500/10">
        <CircleAlertIcon className="mt-0.5 size-4 shrink-0 text-red-500/80" />
        <ErrorPrimitive.Message className="text-red-600 dark:text-red-400" />
      </ErrorPrimitive.Root>
    </MessagePrimitive.Error>
  );
}

```

## Standalone (no runtime)

Standalone, `ErrorState` switches its whole layout on `retrying`: a two-line alert with a title and detail, or a single spinning status line.

```tsx
"use client";

import { useState } from "react";
import { ErrorState } from "@/components/assistant-ui/elements/error-state";

export function ReplyError() {
  const [retrying, setRetrying] = useState(false);
  const [failed, setFailed] = useState(true);

  if (!failed) return null;

  return (
    <ErrorState
      title="Couldn't reach the model"
      detail="The request timed out after 30 seconds."
      retrying={retrying}
      onRetry={async () => {
        setRetrying(true);
        const ok = await retry();
        setRetrying(false);
        setFailed(!ok);
      }}
    />
  );
}

```
