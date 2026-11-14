# Composer trigger popover  (`composer-trigger-popover`)

A character-triggered picker for mentions, slash commands, and nested composer actions.

- 完整文件：`references/pages/composer-trigger-popover.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/composer-trigger-popover.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔

## With a runtime

Place `ComposerPrimitive.TriggerPopoverRoot` around the composer. Any number of `ComposerTriggerPopover` declarations can live inside, each with its own trigger character, adapter, and behavior.

```tsx
import { ComposerPrimitive } from "@assistant-ui/react";

const Composer = () => (
  <ComposerPrimitive.TriggerPopoverRoot>
    <ComposerPrimitive.Root>
      <ComposerPrimitive.Input placeholder="Type @ to mention..." />
      <ComposerPrimitive.Send />
      {/* triggers declared here */}
    </ComposerPrimitive.Root>
  </ComposerPrimitive.TriggerPopoverRoot>
);

```

## Standalone (no runtime)

Trigger detection reads the composer's live text and cursor position from the runtime, so there is no standalone lane: build a character-triggered popover from your own input's `onChange` / `onKeyDown` handlers and any popover primitive.
