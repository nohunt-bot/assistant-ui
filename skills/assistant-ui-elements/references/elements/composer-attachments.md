# Attachments  (`composer-attachments`)

Files stage inside the composer with per-file progress before the message sends.

- 完整文件：`references/pages/composer-attachments.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-composer.md`

## With a runtime

A composer stages files the same way it stages text: `aui.composer.addAttachment(file)` adds one, an `AttachmentAdapter` turns it into a `PendingAttachment` and then a `CompleteAttachment`, and `s.composer.attachments` reflects the list at every step.

```tsx
import { useLocalRuntime, CompositeAttachmentAdapter, SimpleImageAttachmentAdapter, SimpleTextAttachmentAdapter } from "@assistant-ui/react";

const runtime = useLocalRuntime(chatModel, {
  adapters: {
    attachments: new CompositeAttachmentAdapter([
      new SimpleImageAttachmentAdapter(),
      new SimpleTextAttachmentAdapter(),
    ]),
  },
});

```

## Standalone (no runtime)

Standalone, a chip is a pure function of a `ComposerAttachment` object: nothing here uploads a file or tracks progress on its own. You own the array and mutate it as an upload proceeds.

```tsx
"use client";

import { useState } from "react";
import { ComposerAttachments, ComposerAttachmentChip, type ComposerAttachment } from "@/components/assistant-ui/elements/composer";

export function ChatBox() {
  const [attachments, setAttachments] = useState<ComposerAttachment[]>([]);

  const remove = (name: string) =>
    setAttachments((prev) => prev.filter((a) => a.name !== name));

  return (
    <ComposerAttachments>
      {attachments.map((attachment) => (
        <ComposerAttachmentChip key={attachment.name} attachment={attachment} onRemove={remove} />
      ))}
    </ComposerAttachments>
  );
}

```
