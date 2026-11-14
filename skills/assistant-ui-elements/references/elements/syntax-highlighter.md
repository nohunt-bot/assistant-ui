# Syntax highlighter  (`syntax-highlighter`)

Prism-based code highlighting for assistant markdown code blocks.

- 完整文件：`references/pages/syntax-highlighter.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/syntax-highlighter.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔

## With a runtime

const defaultComponents = memoizeMarkdownComponents({
     SyntaxHighlighter,
     h1: /* ... */,
     // ...other elements...
   });
   ```

```tsx
import { SyntaxHighlighter } from "./syntax-highlighter";

const defaultComponents = memoizeMarkdownComponents({
  SyntaxHighlighter,
  h1: /* ... */,
  // ...other elements...
});

```

## Standalone (no runtime)

<SyntaxHighlighter
     language="tsx"
     code="const x = 1;"
     components={{
       Pre: (props) => <pre {...props} />,
       Code: (props) => <code {...props} />,
     }}
   />;
   ```

```tsx
import { SyntaxHighlighter } from "@/components/assistant-ui/elements/syntax-highlighter";

<SyntaxHighlighter
  language="tsx"
  code="const x = 1;"
  components={{
    Pre: (props) => <pre {...props} />,
    Code: (props) => <code {...props} />,
  }}
/>;

```
