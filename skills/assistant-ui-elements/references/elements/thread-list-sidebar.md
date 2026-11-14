# Thread list sidebar  (`thread-list-sidebar`)

A complete sidebar shell that places the runtime thread list beside the active conversation.

- 完整文件：`references/pages/thread-list-sidebar.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/thread.md`、`source/<flavor>/aui/threadlist-sidebar.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔

## With a runtime

`ThreadListSidebar` needs a runtime provider and the sidebar shell's own layout provider around it.

```tsx
import { SidebarInset, SidebarProvider } from "@/components/ui/sidebar";
import { ThreadListSidebar } from "@/components/assistant-ui/elements/threadlist-sidebar.aui";
import { Thread } from "@/components/assistant-ui/elements/thread.aui";

export default function Layout() {
  return (
    <SidebarProvider>
      <ThreadListSidebar />
      <SidebarInset>
        <Thread />
      </SidebarInset>
    </SidebarProvider>
  );
}

```

## Standalone (no runtime)

Thread list sidebar has no standalone form: it renders the runtime-only thread list internally, with no prop to swap in a static one. For a non-runtime sidebar, compose `Sidebar` yourself around the standalone lane of [Thread list](/elements/thread-list).
