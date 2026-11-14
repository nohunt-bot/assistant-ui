# Chat panel  (`chat-panel`)

The whole family working together: a message, a pause, a streamed reply.

- 完整文件：`references/pages/chat-panel.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-chat-panel.md`
- Thread 內建：assistant-ui's `Thread` element already ships a complete version of this composition, wired end to end from viewport to composer.

## With a runtime

assistant-ui's `Thread` element already ships a complete version of this composition, wired end to end from viewport to composer. Build it from these pieces yourself only when you want a smaller, custom shell instead of the full `Thread`.

```tsx
"use client";

import { ThreadPrimitive, MessagePrimitive } from "@assistant-ui/react";
import { ChatPanel, ChatPanelMessages, ChatPanelUserMessage, ChatPanelAssistantMessage } from "./chat-panel";

export function LiveChatPanel() {
  return (
    <ChatPanel>
      <ThreadPrimitive.Viewport asChild>
        <ChatPanelMessages>
          <ThreadPrimitive.Messages>
            {({ message }) =>
              message.role === "user" ? (
                <ChatPanelUserMessage>
                  <MessagePrimitive.Parts />
                </ChatPanelUserMessage>
              ) : (
                <ChatPanelAssistantMessage>
                  <MessagePrimitive.Parts />
                </ChatPanelAssistantMessage>
              )
            }
          </ThreadPrimitive.Messages>
        </ChatPanelMessages>
      </ThreadPrimitive.Viewport>
    </ChatPanel>
  );
}

```

## Standalone (no runtime)

Standalone, you hold the transcript yourself and hand each turn to the matching piece.

```tsx
"use client";

import {
  ChatPanel,
  ChatPanelMessages,
  ChatPanelUserMessage,
  ChatPanelAssistantMessage,
  ChatPanelTyping,
  ChatPanelComposer,
} from "@/components/assistant-ui/elements/chat-panel";

export function Panel() {
  return (
    <ChatPanel>
      <ChatPanelMessages>
        <ChatPanelUserMessage>What's the capital of France?</ChatPanelUserMessage>
        <ChatPanelAssistantMessage>Paris.</ChatPanelAssistantMessage>
        <ChatPanelTyping />
      </ChatPanelMessages>
      <ChatPanelComposer placeholder="Message..." onSend={() => {}} />
    </ChatPanel>
  );
}

```
