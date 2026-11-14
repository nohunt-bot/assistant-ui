# LaTeX in Chat Messages
URL: /docs/guides/latex

Render LaTeX math expressions in AI chat messages with KaTeX — drop-in equation support for React chat UIs built on assistant-ui.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

Render LaTeX mathematical expressions in chat messages using KaTeX.

> [!warn]
>
> LaTeX rendering is not enabled by default.

## Setup

To set this up, run `npx assistant-ui@latest setup --products guides/latex` if available; otherwise follow these steps.

Choose one:

**react-markdown**

1. ### Install dependencies

   - packages

     - katex
     - rehype-katex
     - remark-math

2. ### Add KaTeX CSS to your layout

   ```
   import "katex/dist/katex.min.css";
   ```

3. ### Update `markdown-text.tsx`

   ```
   import { MarkdownTextPrimitive } from "@assistant-ui/react-markdown";
   import remarkMath from "remark-math";
   import rehypeKatex from "rehype-katex";

   const MarkdownTextImpl = () => {
     return (
       <MarkdownTextPrimitive
         remarkPlugins={[remarkGfm, remarkMath]} // add remarkMath
         rehypePlugins={[rehypeKatex]}           // add rehypeKatex
         className="aui-md"
         components={defaultComponents}
       />
     );
   };

   export const MarkdownText = memo(MarkdownTextImpl);
   ```

**Streamdown**

> [!info]
>
> Using [Streamdown](/docs/guides/streamdown) as your renderer? Math support is a first-party plugin, so you do not need remark or rehype packages.

1. ### Install dependencies

   - packages

     - @streamdown/math
     - katex

2. ### Add KaTeX CSS to your layout

   ```
   import "katex/dist/katex.min.css";
   ```

3. ### Pass the `math` plugin to `StreamdownTextPrimitive`

   ```
   import { math } from "@streamdown/math";
   import "katex/dist/katex.min.css";

   <StreamdownTextPrimitive plugins={{ math }} />
   ```

## Supported Formats

By default, remark-math (react-markdown path) supports:

- `$...$` for inline math
- `$$...$$` for display math
- Fenced code blocks with the `math` language identifier

## Supporting Alternative LaTeX Delimiters

Many language models emit math in delimiters that remark-math does not recognize:

- `\(...\)` for inline math and `\[...\]` for display math
- custom tags like `[/math]...[/math]` and `[/inline]...[/inline]`

`@assistant-ui/react-markdown` exports `normalizeMathDelimiters`, which rewrites these to the `$...$` and `$$...$$` form remark-math parses. It also indents a `$$` block that a model opens on a list item's marker line, in a blockquote or not, but writes without the item's indentation, so the formula stays inside its item. Pass it to the `preprocess` prop of `MarkdownTextPrimitive`:

```
import {
  MarkdownTextPrimitive,
  normalizeMathDelimiters,
} from "@assistant-ui/react-markdown";

const MarkdownTextImpl = () => {
  return (
    <MarkdownTextPrimitive
      remarkPlugins={[remarkGfm, remarkMath]}
      rehypePlugins={[rehypeKatex]}
      preprocess={normalizeMathDelimiters}
      className="aui-md"
      components={defaultComponents}
    />
  );
};
```

The individual transforms `rewriteLatexBracketDelimiters` and `rewriteCustomMathTags` are exported too, for finer control over which delimiters are normalized.

> [!info]
>
> Using [Streamdown](/docs/guides/streamdown) as your renderer? The same helpers are exported from `@assistant-ui/react-streamdown` and accepted by the `preprocess` prop of `StreamdownTextPrimitive`.

### Currency amounts

With single-dollar inline math enabled (the default on the react-markdown path), remark-math reads a lone `$` as a math delimiter, so prose such as `$5 ... $10` is parsed as math. `escapeCurrencyDollars` escapes a `$` immediately followed by a digit so currency survives, while leaving the `$$` of display math intact. Compose it with the delimiter normalization:

```
import {
  normalizeMathDelimiters,
  escapeCurrencyDollars,
} from "@assistant-ui/react-markdown";

<MarkdownTextPrimitive
  preprocess={(text) => escapeCurrencyDollars(normalizeMathDelimiters(text))}
  // ...
/>;
```

> [!tip]
>
> Inside `MarkdownTextPrimitive`, `useSmooth` reveals the raw accumulated text character by character. `preprocess` then normalizes only the revealed prefix before it reaches the markdown parser. Both run before remark-math sees the text, so a partially received delimiter remains raw until its closing token has been revealed.