# Image  (`image`)

Image message parts with preview, loading states, actions, and a fullscreen view.

- 完整文件：`references/pages/image.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/image.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- Thread 內建：The `Thread` element already renders both assistant and user image parts through `Image`, so installing `@assistant-ui/thread` gives you this without any of the above.

## With a runtime

Register `Image` as the `Image` renderer on `MessagePrimitive.Parts`.

```tsx
import { Image } from "@/components/assistant-ui/elements/image";
import { MessagePrimitive } from "@assistant-ui/react";

function AssistantMessage() {
  return (
    <MessagePrimitive.Root>
      <MessagePrimitive.Parts components={{ Image }} />
    </MessagePrimitive.Root>
  );
}

```

## Standalone (no runtime)

Standalone, `Image` is a plain function of one part object: no context, no runtime.

```tsx
import { Image } from "@/components/assistant-ui/elements/image";

export function Picture() {
  return (
    <Image
      type="image"
      image="https://example.com/photo.png"
      filename="photo.png"
      status={{ type: "complete" }}
    />
  );
}

```
