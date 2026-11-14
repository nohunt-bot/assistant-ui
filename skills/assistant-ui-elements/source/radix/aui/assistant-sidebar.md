# Assistant Sidebar（`aui/assistant-sidebar`，radix）

Side panel chat for copilot experiences and inline assistance.

- 直接依賴：`ui/resizable`, `aui/thread`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `ui/resizable`, `ui/button`, `ui/skeleton`, `ui/dialog`, `ui/tooltip`, `ui/avatar`, `aui/tooltip-icon-button`, `aui/use-attachment-src`, `aui/elements-attachment-state`, `aui/attachment`, `aui/elements-surfaces`, `aui/elements-media-player`, `aui/file`, `aui/follow-up-suggestions`, `aui/elements-href`, `aui/image`, `aui/use-copy-to-clipboard`, `aui/markdown-text`, `aui/elements-collapsible-root`, `aui/elements-reasoning`, `aui/reasoning`, `ui/textarea`, `aui/tool-fallback`, `aui/tool-group`, `aui/thread`, `aui/assistant-sidebar`
- npm（整組）：`npm install cn react-resizable-panels@^4 radix-ui @assistant-ui/react@^0.15.36 zustand lucide-react tw-shimmer@^0.4.14 class-variance-authority @assistant-ui/react-markdown@^0.14.26 remark-gfm`

## 檔案：`components/assistant-ui/elements/assistant-sidebar.aui.tsx`

```tsx
import {
  ResizableHandle,
  ResizablePanel,
  ResizablePanelGroup,
} from "@/components/ui/resizable";
import type { FC, PropsWithChildren } from "react";

import { Thread } from "@/components/assistant-ui/elements/thread.aui";

export const AssistantSidebar: FC<PropsWithChildren> = ({ children }) => {
  return (
    <ResizablePanelGroup orientation="horizontal">
      <ResizablePanel>{children}</ResizablePanel>
      <ResizableHandle />
      <ResizablePanel>
        <Thread />
      </ResizablePanel>
    </ResizablePanelGroup>
  );
};
```
