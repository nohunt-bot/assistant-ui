# Mermaid diagram  (`mermaid-diagram`)

Mermaid diagrams rendered inside messages, including partial streaming input.

- 完整文件：`references/pages/mermaid-diagram.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/mermaid-diagram.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-mermaid-diagram.md`

## With a runtime

The kit wraps the base diagram and reads whether the current message part is still running, so the model can keep emitting `mermaid` syntax without a half-drawn diagram flashing on every token.

```tsx
import { MermaidDiagram } from "@/components/assistant-ui/elements/mermaid-diagram.aui";

const MarkdownTextImpl = () => {
  return (
    <MarkdownTextPrimitive
      remarkPlugins={[remarkGfm]}
      className="aui-md"
      components={defaultComponents}
      componentsByLanguage={{
        mermaid: { SyntaxHighlighter: MermaidDiagram },
      }}
    />
  );
};

```

## Standalone (no runtime)

Standalone, you own the streaming flag yourself.

```tsx
import { MermaidDiagram } from "@/components/assistant-ui/elements/mermaid-diagram";

<MermaidDiagram code={code} streaming={isStreaming} />;

```
