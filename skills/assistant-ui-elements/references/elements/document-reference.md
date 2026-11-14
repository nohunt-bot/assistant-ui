# Document reference  (`document-reference`)

A document the answer leans on, with the quoted passage and the page to jump to.

- 完整文件：`references/pages/document-reference.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-document-reference.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-document-reference.md`

## With a runtime

assistant-ui's built-in `source` message part covers a single document citation (a title, a filename), but not a list of page anchors with quoted passages. The closest real building block for that shape is a tool call: ask the model to read a document and return its anchors, then render the result.

```tsx
"use client";

import { useState } from "react";
import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import {
  DocumentReference,
  type DocumentAnchor,
} from "@/components/assistant-ui/elements/document-reference";

type ReadDocumentResult = {
  title: string;
  pages: number;
  anchors: DocumentAnchor[];
};

export const ReadDocumentToolUI: ToolCallMessagePartComponent<
  { query: string },
  ReadDocumentResult
> = ({ result }) => {
  const [activePage, setActivePage] = useState<number>();

  if (!result) return null;
  const page = activePage ?? result.anchors[0]?.page ?? 1;

  return (
    <DocumentReference
      title={result.title}
      pages={result.pages}
      anchors={result.anchors}
      activePage={page}
      onJump={setActivePage}
    />
  );
};

```

## Standalone (no runtime)

Standalone, you hold the anchor list and the active page, and the element reports jumps back to you. Which of several passages on one page the user clicked is the only thing it tracks itself.

```tsx
"use client";

import { useState } from "react";
import { DocumentReference } from "@/components/assistant-ui/elements/document-reference";

const ANCHORS = [
  { page: 4, quote: "Composer state moved off the thread runtime in 0.14." },
  { page: 9, quote: "Existing drafts migrate on first read; nothing to run by hand." },
];

export function Citation() {
  const [activePage, setActivePage] = useState(ANCHORS[0].page);
  return (
    <DocumentReference
      title="migration-0.14.md"
      pages={12}
      anchors={ANCHORS}
      activePage={activePage}
      onJump={setActivePage}
    />
  );
}

```
