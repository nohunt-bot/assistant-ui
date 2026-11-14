# Mermaid Diagram（`aui/mermaid-diagram`，base）

Render Mermaid diagrams in messages, including while they stream.

- 直接依賴：`aui/elements-mermaid-diagram`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/elements-mermaid-diagram`, `aui/mermaid-diagram`
- npm（整組）：`npm install cn beautiful-mermaid lucide-react @assistant-ui/react@^0.15.36 @assistant-ui/react-markdown@^0.14.26`

## 檔案：`components/assistant-ui/elements/mermaid-diagram.aui.tsx`

```tsx
"use client";

import { memo, type FC } from "react";
import { useAuiState } from "@assistant-ui/react";
import type { SyntaxHighlighterProps } from "@assistant-ui/react-markdown";
import {
  MermaidDiagram as MermaidDiagramBase,
  MermaidZoom,
} from "./mermaid-diagram";

export type MermaidDiagramProps = SyntaxHighlighterProps & {
  className?: string;
};

/**
 * Use it by passing to `componentsByLanguage` for mermaid in `markdown-text.tsx`.
 *
 * @example
 * const MarkdownTextImpl = () => {
 *   return (
 *     <MarkdownTextPrimitive
 *       remarkPlugins={[remarkGfm]}
 *       className="aui-md"
 *       components={defaultComponents}
 *       componentsByLanguage={{
 *         mermaid: {
 *           SyntaxHighlighter: MermaidDiagram
 *         },
 *       }}
 *     />
 *   );
 * };
 */
const MermaidDiagramImpl: FC<MermaidDiagramProps> = ({
  code,
  className,
  node: _node,
  components: _components,
  language: _language,
}) => {
  const isStreaming = useAuiState(
    (s) => s.optional.part?.status.type === "running",
  );

  return (
    <MermaidDiagramBase
      code={code}
      {...(className !== undefined ? { className } : {})}
      streaming={isStreaming}
    />
  );
};

const MermaidDiagram = memo(
  MermaidDiagramImpl,
) as unknown as FC<MermaidDiagramProps> & {
  Zoom: typeof MermaidZoom;
};

MermaidDiagram.displayName = "MermaidDiagram";
MermaidDiagram.Zoom = MermaidZoom;

export { MermaidDiagram, MermaidZoom };
```
