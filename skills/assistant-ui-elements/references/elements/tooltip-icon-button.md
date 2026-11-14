# Tooltip icon button  (`tooltip-icon-button`)

An accessible icon button with a tooltip label and shared interaction states.

- 完整文件：`references/pages/tooltip-icon-button.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/tooltip-icon-button.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔

## With a runtime

Every action button across the runtime-connected kit elements, thread, voice, attachment, the assistant modal, is a `TooltipIconButton`, usually composed with a primitive through `render` so the primitive owns the click handling and the disabled or hidden state.

```tsx
"use client";

import { ThreadPrimitive } from "@assistant-ui/react";
import { ArrowDownIcon } from "lucide-react";
import { TooltipIconButton } from "@/components/assistant-ui/elements/tooltip-icon-button";

function ScrollToBottom() {
  return (
    <ThreadPrimitive.ScrollToBottom
      render={<TooltipIconButton tooltip="Scroll to bottom" variant="outline" />}
    >
      <ArrowDownIcon />
    </ThreadPrimitive.ScrollToBottom>
  );
}

```

## Standalone (no runtime)

Standalone, there's no primitive to compose it with, so you own the click handler, the disabled state, and, if the action can fail or take time, any loading state yourself.

```tsx
"use client";

import { useState } from "react";
import { SparklesIcon } from "lucide-react";
import { TooltipIconButton } from "@/components/assistant-ui/elements/tooltip-icon-button";

export function GenerateButton() {
  const [busy, setBusy] = useState(false);
  return (
    <TooltipIconButton
      tooltip="Generate"
      variant="outline"
      disabled={busy}
      onClick={async () => {
        setBusy(true);
        await generate();
        setBusy(false);
      }}
    >
      <SparklesIcon />
    </TooltipIconButton>
  );
}

```
