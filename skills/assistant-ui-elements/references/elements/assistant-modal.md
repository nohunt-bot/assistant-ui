# Assistant modal  (`assistant-modal`)

A floating chat bubble for support widgets, help desks, and embedded assistants, with a thread list and a resizable window.

- 完整文件：`references/pages/assistant-modal.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/assistant-modal.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔

## With a runtime

`AssistantModal` needs nothing beyond a runtime provider higher up the tree; it positions and opens itself.

```tsx
import { AssistantModal } from "@/components/assistant-ui/elements/assistant-modal.aui";

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <>
      {children}
      <AssistantModal />
    </>
  );
}

```

## Standalone (no runtime)

Assistant modal has no standalone form: it composes the runtime-only [Thread](/elements/thread) inside a popover it opens itself, tied to the runtime's `thread.runStart` event. Build your own floating trigger around a popover and Thread's primitives if you need different open behavior.
