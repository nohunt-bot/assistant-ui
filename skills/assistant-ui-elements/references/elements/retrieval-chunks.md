# Retrieval chunks  (`retrieval-chunks`)

The passages a retrieval answer stands on, scored, before the answer itself arrives.

- 完整文件：`references/pages/retrieval-chunks.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-retrieval-chunks.md`、`source/<flavor>/aui/thread.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-retrieval-chunks.md`

## With a runtime

A retrieval tool renders through that tool's own toolkit entry: the model's query arrives as `args`, the retrieved passages as `result`, and the call's lifecycle as `status`.

```tsx
"use generative";

import { defineToolkit, externalTool } from "@assistant-ui/react";
import { RetrievalChunks } from "@/components/assistant-ui/elements/retrieval-chunks";

export default defineToolkit({
  search_docs: {
    execute: externalTool(),
    render: ({ args, result, status }) => (
      <RetrievalChunks
        query={args.query ?? ""}
        chunks={result?.chunks ?? []}
        visibleCount={result?.chunks.length ?? 0}
        searching={status.type === "running"}
      />
    ),
  },
});

```

## Standalone (no runtime)

Standalone, `RetrievalChunks` is fully controlled: you hold the query, the full chunk list, and how many of them are visible.

```tsx
"use client";

import { useState } from "react";
import {
  RetrievalChunks,
  type RetrievalChunk,
} from "@/components/assistant-ui/elements/retrieval-chunks";

const CHUNKS: readonly RetrievalChunk[] = [
  {
    id: "c1",
    source: "migration-0.14.md",
    locator: "§ 2",
    score: 0.91,
    text: "The composer owns its draft from 0.14 onward.",
  },
  {
    id: "c2",
    source: "composer.tsx",
    locator: "L12",
    score: 0.84,
    text: "useDraft(threadId) subscribes to the per-thread draft slot.",
  },
];

export function Retrieval() {
  const [searching, setSearching] = useState(true);
  const [visibleCount, setVisibleCount] = useState(0);

  return (
    <RetrievalChunks
      query="how does draft restore work"
      chunks={CHUNKS}
      visibleCount={visibleCount}
      searching={searching}
    />
  );
}

```
