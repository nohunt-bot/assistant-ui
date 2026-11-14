# File  (`file`)

File message parts with type-aware icons, inline media playback, filename, size, and download actions.

- 完整文件：`references/pages/file.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/file.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- Thread 內建：The `Thread` element already renders both assistant and user file parts through `File`, so installing `@assistant-ui/thread` gives you this without any of the above.

## With a runtime

Register `File` as the `File` renderer on `MessagePrimitive.Parts`.

```tsx
import { File } from "@/components/assistant-ui/elements/file";
import { MessagePrimitive } from "@assistant-ui/react";

function AssistantMessage() {
  return (
    <MessagePrimitive.Root>
      <MessagePrimitive.Parts components={{ File }} />
    </MessagePrimitive.Root>
  );
}

```

## Standalone (no runtime)

Standalone, `File` is a plain function of one part object: no context, no runtime.

```tsx
import { File } from "@/components/assistant-ui/elements/file";

export function Attachment() {
  return (
    <File
      type="file"
      filename="report.pdf"
      mimeType="application/pdf"
      data="https://example.com/report.pdf"
      sourceType="url"
      status={{ type: "complete" }}
    />
  );
}

```
