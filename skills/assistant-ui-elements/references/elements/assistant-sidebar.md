# Assistant sidebar  (`assistant-sidebar`)

A resizable side panel for copilot experiences and contextual assistance.

- 完整文件：`references/pages/assistant-sidebar.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/assistant-sidebar.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔

## With a runtime

`AssistantSidebar` needs nothing beyond a runtime provider higher up the tree and the content to show in the left pane.

```tsx
import { AssistantSidebar } from "@/components/assistant-ui/elements/assistant-sidebar.aui";

export default function Layout({ children }: { children: React.ReactNode }) {
  return <AssistantSidebar>{children}</AssistantSidebar>;
}

```

## Standalone (no runtime)

Assistant sidebar has no standalone form: its only prop is `children`, and the conversation pane is always the runtime-only Thread. For a resizable split with a static or otherwise non-runtime right pane, compose `ResizablePanelGroup` directly around your own content.
