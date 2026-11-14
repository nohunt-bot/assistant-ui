# Message pair  (`message-pair`)

A user bubble and a streaming assistant reply, with actions that appear on hover.

- 完整文件：`references/pages/message-pair.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-message-pair.md`

## With a runtime

A thread renders as a list of messages, each one either from the user or the assistant. Compose the pair from `MessagePrimitive.Root` per message and an action row that only shows on hover.

```tsx
"use client";

import { MessagePrimitive, ThreadPrimitive, useAuiState } from "@assistant-ui/react";
import { cn } from "@/lib/utils";
import { paper } from "@/components/assistant-ui/elements/surfaces";

export function Turn() {
  return <ThreadPrimitive.Messages>{() => <TurnMessage />}</ThreadPrimitive.Messages>;
}

function TurnMessage() {
  const role = useAuiState((s) => s.message.role);
  return (
    <MessagePrimitive.Root className="flex w-full flex-col gap-5">
      {role === "user" ? (
        <div className={cn(paper, "max-w-[85%] self-end rounded-2xl px-3.5 py-2 text-sm")}>
          <MessagePrimitive.Parts />
        </div>
      ) : (
        <AssistantReply />
      )}
    </MessagePrimitive.Root>
  );
}

```

## Standalone (no runtime)

Standalone, the pair is a controlled reveal: you hold the full list of words and how many are currently shown, and the element handles the fade-in and the trailing cursor.

```tsx
"use client";

import { useEffect, useState } from "react";
import { MessagePair } from "@/components/assistant-ui/elements/message-pair";

const reply = "Paris is the capital of France.".split(" ");

export function Turn() {
  const [visibleWords, setVisibleWords] = useState(0);
  const streaming = visibleWords < reply.length;

  useEffect(() => {
    if (!streaming) return;
    const id = setInterval(() => setVisibleWords((n) => n + 1), 120);
    return () => clearInterval(id);
  }, [streaming]);

  return (
    <MessagePair
      userMessage="What's the capital of France?"
      words={reply}
      visibleWords={visibleWords}
      streaming={streaming}
      onCopy={() => void navigator.clipboard?.writeText(reply.join(" "))}
      onRegenerate={() => setVisibleWords(0)}
    />
  );
}

```
