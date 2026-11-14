# Shared conversation  (`shared-conversation`)

A read-only transcript someone sent you, with a way to pick it up yourself.

- 完整文件：`references/pages/shared-conversation.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-shared-conversation.md`

## With a runtime

`ReadonlyThreadProvider` wraps children in a read-only thread. It reports itself disabled, so a composer inside renders disabled; thread mutations are ignored, and composer text, sending, adding attachments, and setting a quote have no effect. The normal thread primitives render it exactly like any other thread.

```tsx
"use client";

import { ReadonlyThreadProvider, ThreadPrimitive, MessagePrimitive } from "@assistant-ui/react";
import type { ThreadMessage } from "@assistant-ui/react";

export function SharedThread({ messages }: { messages: readonly ThreadMessage[] }) {
  return (
    <ReadonlyThreadProvider messages={messages}>
      <ThreadPrimitive.Viewport>
        <ThreadPrimitive.Messages>
          {() => <MessagePrimitive.Parts />}
        </ThreadPrimitive.Messages>
      </ThreadPrimitive.Viewport>
    </ReadonlyThreadProvider>
  );
}

```

## Standalone (no runtime)

Standalone, `SharedConversation` is a self-contained card: title, who shared it, the turns, and the continue button, all from props.

```tsx
"use client";

import { SharedConversation } from "@/components/assistant-ui/elements/shared-conversation";

export function SharedCard({ onContinue }: { onContinue: () => void }) {
  return (
    <SharedConversation
      title="Debugging the flaky test"
      sharedBy="Jordan"
      sharedAt="2 days ago"
      turns={[
        { id: "1", role: "user", text: "Why does this test fail only in CI?" },
        { id: "2", role: "assistant", text: "Most likely a timing assumption..." },
      ]}
      onContinue={onContinue}
    />
  );
}

```
