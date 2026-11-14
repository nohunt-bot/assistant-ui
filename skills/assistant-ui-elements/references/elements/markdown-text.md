# Markdown text  (`markdown-text`)

Assistant markdown with headings, lists, links, tables, and code blocks.

- 完整文件：`references/pages/markdown-text.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/markdown-text.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- Thread 內建：The `Thread` element already ships this composition, so installing `@assistant-ui/thread` gives you formatted assistant messages without wiring MarkdownText yourself.

## With a runtime

MarkdownText reads the current message part through `MarkdownTextPrimitive`, which pulls the streaming text straight from the part's context. It must render inside a text part, so it is wired in as the renderer for that part type rather than mounted on its own.

```tsx
import { MessagePrimitive } from "@assistant-ui/react";
import { MarkdownText } from "@/components/assistant-ui/elements/markdown-text";

function AssistantMessage() {
  return (
    <MessagePrimitive.Root>
      <MessagePrimitive.Parts components={{ Text: MarkdownText }} />
    </MessagePrimitive.Root>
  );
}

```

## Standalone (no runtime)

Standalone, MarkdownText has no controlled form: its only props are `components` and `componentsByLanguage`, and the text it renders always comes from the surrounding message part, never from a value you pass in. To render markdown outside a message part, render `react-markdown` directly instead, the way [Generative UI](/elements/generative-ui)'s `Markdown` override does.
