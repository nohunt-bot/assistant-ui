# Mobile composer  (`mobile-composer`)

The bottom sheet: keyboard-aware, quick actions above, thumb-sized targets.

- 完整文件：`references/pages/mobile-composer.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-mobile-composer.md`

## With a runtime

`ComposerPrimitive.Root`, `.Input`, `.AddAttachment`, `.Send`, and `.Cancel` already own the text, the attach flow, and the send/cancel gating, so this bar needs no `value`, `onValueChange`, `onSend`, or `onStop`. It needs only the layout and the running flag to pick between Send and Cancel.

```tsx
"use client";

import { ArrowUpIcon, MicIcon, PlusIcon, SquareIcon } from "lucide-react";
import { ComposerPrimitive, ThreadPrimitive, useAuiState } from "@assistant-ui/react";
import { cn } from "@/lib/utils";
import { field, ghostButton, inkButton, mono } from "@/components/assistant-ui/elements/surfaces";

const ACTIONS = ["Summarize", "Translate", "Explain"];

export function MobileComposerBar({ keyboardOpen }: { keyboardOpen: boolean }) {
  const isRunning = useAuiState((s) => s.thread.isRunning);
  const isEmpty = useAuiState((s) => s.composer.isEmpty);

  return (
    <ComposerPrimitive.Root
      className={cn(
        "bg-background border-foreground/[0.07] flex w-full max-w-[19rem] flex-col gap-2.5 rounded-t-[20px] border-t px-3 pt-3",
        keyboardOpen ? "pb-3" : "pb-6",
      )}
    >
      {!keyboardOpen && (
        <div className="-mx-3 flex gap-1.5 overflow-x-auto px-3 pb-0.5">
          {ACTIONS.map((action) => (
            <ThreadPrimitive.Suggestion
              key={action}
              prompt={action}
              className={cn(field, "text-foreground/60 shrink-0 rounded-full px-3 py-1.5 text-xs whitespace-nowrap")}
            >
              {action}
            </ThreadPrimitive.Suggestion>
          ))}
        </div>
      )}

      <div className="flex items-end gap-2">
        <ComposerPrimitive.AddAttachment className={cn(ghostButton, field, "size-9 shrink-0")}>
          <PlusIcon className="size-4" />
        </ComposerPrimitive.AddAttachment>

        <div className={cn(field, "flex min-w-0 flex-1 items-center gap-2 rounded-[18px] px-3 py-2")}>
          <ComposerPrimitive.Input
            placeholder="Message"
            className="text-foreground/85 placeholder:text-muted-foreground min-w-0 flex-1 resize-none bg-transparent text-[16px] outline-none"
          />
          {isEmpty && <MicIcon className="text-muted-foreground size-4 shrink-0" />}
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, every field is a prop and the component owns exactly two pieces of logic: Enter submits when there is text and no run is in flight, and the send button disables under the same condition.

```tsx
"use client";

import { useState } from "react";
import { MobileComposer } from "@/components/assistant-ui/elements/mobile-composer";

export function MobileBar() {
  const [value, setValue] = useState("");
  const [running, setRunning] = useState(false);
  const [keyboardOpen, setKeyboardOpen] = useState(false);

  return (
    <MobileComposer
      value={value}
      keyboardOpen={keyboardOpen}
      running={running}
      actions={["Summarize", "Translate", "Explain"]}
      onAction={(action) => setValue(action)}
      onAttach={() => console.log("attach")}
      onValueChange={setValue}
      onSend={() => {
        setRunning(true);
        setValue("");
      }}
      onStop={() => setRunning(false)}
      onFocus={() => setKeyboardOpen(true)}
    />
  );
}

```
