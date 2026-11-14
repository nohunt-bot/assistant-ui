# Artifact card  (`artifact-card`)

A generated document as a tangible object, written live and versioned.

- 完整文件：`references/pages/artifact-card.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-artifact-card.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-artifact-card.md`

## With a runtime

A document like this is a tool call whose result is the document itself: the call's `status` says whether it is still writing, and its `args` carry the title, the running word count, and the caption to show once it settles.

```tsx
"use client";

import { defineToolkit } from "@assistant-ui/react";
import { ArtifactCard } from "@/components/assistant-ui/elements/artifact-card";

export const toolkit = defineToolkit({
  write_document: {
    type: "backend",
    render: ({ args, status }) => (
      <ArtifactCard
        title={args.title}
        meta={args.meta}
        generating={status.type === "running"}
        words={args.words ?? 0}
      />
    ),
  },
});

```

## Standalone (no runtime)

Standalone, the element is a controlled display: you own `generating` and `words`, and flip them as the document progresses.

```tsx
"use client";

import { useState } from "react";
import { ArtifactCard } from "@/components/assistant-ui/elements/artifact-card";

export function Artifact() {
  const [generating, setGenerating] = useState(true);
  const [words, setWords] = useState(0);

  return (
    <ArtifactCard
      title="Draft persistence RFC"
      meta="Document · v3 · just now"
      generating={generating}
      words={words}
    />
  );
}

```
