# Shiki highlighter  (`shiki-highlighter`)

Shiki code highlighting that defers tokenization until a message part settles.

- 完整文件：`references/pages/shiki-highlighter.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/shiki-highlighter.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-shiki-highlighter.md`

## With a runtime

The kit wraps the base highlighter and reads whether the current message part is still running, so it never spends a Shiki pass on code that is about to change again.

```tsx
import { SyntaxHighlighter } from "./shiki-highlighter.aui";

const defaultComponents = memoizeMarkdownComponents({
  SyntaxHighlighter,
  h1: /* ... */,
  // ...other elements...
});

```

## Standalone (no runtime)

Standalone, you own the streaming flag yourself.

```tsx
import { SyntaxHighlighter } from "@/components/assistant-ui/elements/shiki-highlighter";

<SyntaxHighlighter language="tsx" code={code} streaming={isStreaming} />;

```
