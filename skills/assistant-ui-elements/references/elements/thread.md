# Thread  (`thread`)

A complete chat container with messages, composer, auto-scroll, and accessibility built in.

- 完整文件：`references/pages/thread.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/thread.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔

## With a runtime

`Thread` needs nothing beyond a runtime provider higher up the tree.

```tsx
import { Thread } from "@/components/assistant-ui/elements/thread.aui";

export default function Chat() {
  return (
    <div className="h-full">
      <Thread />
    </div>
  );
}

```

## Standalone (no runtime)

Thread has no props-driven counterpart: the welcome screen, message list, streaming states, and composer are all derived from live thread state, not props you would pass in. Compose the primitives it wraps, `ThreadPrimitive`, `ComposerPrimitive`, and `MessagePrimitive`, directly against your own runtime for a materially different layout; message-level pieces it already ships have their own standalone docs, for example the branch picker on [Message branches](/elements/message-branches) and the reasoning trace on [Reasoning](/elements/reasoning).
