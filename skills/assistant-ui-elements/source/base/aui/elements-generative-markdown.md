# Generative Markdown（`aui/elements-generative-markdown`，base）

Render streamed Markdown and nested generative UI content.

- 直接依賴：`aui/generative-ui-style`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/generative-ui-style`, `aui/elements-generative-markdown`
- npm（整組）：`npm install cn react-markdown remark-gfm`

## 檔案：`components/assistant-ui/elements/generative-markdown.tsx`

```tsx
"use client";

import type { ReactNode } from "react";
import ReactMarkdown from "react-markdown";
import remarkGfm from "remark-gfm";

export type GenerativeMarkdownProps = {
  value?: string | undefined;
  children?: ReactNode;
};

export function GenerativeMarkdown({
  value,
  children,
}: GenerativeMarkdownProps) {
  return (
    <div data-aui="markdown">
      <ReactMarkdown remarkPlugins={[remarkGfm]}>{value ?? ""}</ReactMarkdown>
      {children}
    </div>
  );
}
```
