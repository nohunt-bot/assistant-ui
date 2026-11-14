# Quote  (`quote`)

Select message text, quote it from a floating toolbar, and carry it into the composer.

- 完整文件：`references/pages/quote.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/quote.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔

## With a runtime

Render `MessagePrimitive.Quote` above `MessagePrimitive.Parts` in the user message. It renders nothing until that message carries a quote.

```tsx
import { QuoteBlock } from "@/components/assistant-ui/elements/quote.aui";
import { MessagePrimitive } from "@assistant-ui/react";

function UserMessage() {
  return (
    <MessagePrimitive.Root>
      <MessagePrimitive.Quote>
        {(quote) => <QuoteBlock {...quote} />}
      </MessagePrimitive.Quote>
      <MessagePrimitive.Parts />
    </MessagePrimitive.Root>
  );
}

```

## Standalone (no runtime)

Standalone, build the same flow yourself: hold the quoted text in state, show it above your composer, and clear it on send or dismiss. [The quote-reply design](#the-quote-reply-design) below is a ready-made version of that flow with no runtime dependency.
