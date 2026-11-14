# Launcher  (`launcher-bubble`)

The floating entry point, and the panel it opens into.

- 完整文件：`references/pages/launcher-bubble.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-launcher-bubble.md`

## With a runtime

Nothing about the panel is runtime state except what happens when you press something in it. Rebuild the panel's two interactive pieces from `ThreadPrimitive.Suggestion` and `ComposerPrimitive.Send`, and the launcher drives the same composer and thread the rest of your app does.

```tsx
"use client";

import { useState } from "react";
import { ComposerPrimitive, ThreadPrimitive, useAuiEvent } from "@assistant-ui/react";
import { MessageCircleIcon, XIcon } from "lucide-react";
import { cn } from "@/lib/utils";
import { field, floating, inkButton, mono } from "@/components/assistant-ui/elements/surfaces";

const PROMPTS = ["Summarize this page", "Find a teammate", "Report a bug"];

export function Launcher() {
  const [open, setOpen] = useState(false);

  // Surface the panel the moment a run starts, even if it was triggered elsewhere.
  useAuiEvent("thread.runStart", () => setOpen(true));

  return (
    <div className="flex w-full max-w-[19rem] flex-col items-end gap-2.5">
      {open && (
        <div className={cn(floating, "flex w-full flex-col gap-3 rounded-[20px] p-4")}>
          <div className="flex flex-col gap-1">
            <span className="text-[13.5px] font-medium">How can I help?</span>
            <span className={cn(mono, "text-muted-foreground")}>typically replies in a minute</span>
          </div>

          <div className="flex flex-col gap-1.5">
            {PROMPTS.map((prompt) => (
              <ThreadPrimitive.Suggestion
                key={prompt}
                prompt={prompt}
                className={cn(
                  field,
                  "hover:bg-foreground/[0.07] text-foreground/70 rounded-xl px-3 py-2 text-start text-[13px] transition-colors",
                )}
              >
                {prompt}
              </ThreadPrimitive.Suggestion>
            ))}
          </div>

          <ComposerPrimitive.Send
            className={cn(inkButton, "flex h-8 items-center justify-center rounded-full text-xs font-medium")}
          >
            Start a conversation
          </ComposerPrimitive.Send>
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, `open`, `unread`, picking a prompt, and starting are four plain callbacks; nothing about the panel's content is computed for you.

```tsx
"use client";

import { useState } from "react";
import { LauncherBubble } from "@/components/assistant-ui/elements/launcher-bubble";

const prompts = ["Reset my password", "Talk to a person", "Where is my order?"];

export function SupportLauncher() {
  const [open, setOpen] = useState(false);
  const [unread, setUnread] = useState(2);

  return (
    <LauncherBubble
      open={open}
      unread={unread}
      greeting="Hey, need a hand?"
      prompts={prompts}
      onToggle={() => {
        setOpen((o) => !o);
        setUnread(0);
      }}
      onPick={(prompt) => console.log("picked", prompt)}
      onStart={() => console.log("start")}
    />
  );
}

```
