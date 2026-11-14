# Attachment  (`attachment`)

Runtime attachments for the composer and messages, with previews, progress, and removal.

- 完整文件：`references/pages/attachment.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/attachment.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- Thread 內建：`Thread` already renders every piece below by default; use these directly if you're composing your own layout instead of `Thread`.

## With a runtime

`Thread` already renders every piece below by default; use these directly if you're composing your own layout instead of `Thread`.

```tsx
import { ComposerPrimitive } from "@assistant-ui/react";
import {
  ComposerAddAttachment,
  ComposerAttachments,
} from "@/components/assistant-ui/elements/attachment.aui";

function Composer() {
  return (
    <ComposerPrimitive.Root>
      <ComposerPrimitive.AttachmentDropzone>
        <ComposerAttachments />
        <ComposerPrimitive.Input placeholder="Send a message..." />
        <ComposerAddAttachment />
      </ComposerPrimitive.AttachmentDropzone>
    </ComposerPrimitive.Root>
  );
}

```

## Standalone (no runtime)

Standalone, there is nothing to stage or upload: this element exists to reflect a runtime's own attachment state, including progress and removal, back into the UI. Render a sent message's files with the received-files design instead (see [The received-files design](#the-received-files-design)), which takes plain attachment records as props.
