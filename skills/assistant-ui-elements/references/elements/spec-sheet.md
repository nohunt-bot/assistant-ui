# Spec sheet  (`spec-sheet`)

The most common structured answer after a table: one object, labeled.

- 完整文件：`references/pages/spec-sheet.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-spec-sheet.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-spec-sheet.md`

## With a runtime

This element has no assistant-ui primitive of its own, so the runtime wiring is a tool renderer rather than a primitive composition. Unlike a piece the model authors freely, a spec sheet is usually a lookup, so the rows belong on the tool's `result` rather than on its streamed `args`.

```tsx
"use client";

import { defineToolkit } from "@assistant-ui/react";
import { SpecSheet } from "@/components/assistant-ui/elements/spec-sheet";

export const toolkit = defineToolkit({
  get_model_card: {
    type: "backend",
    render: ({ args, result }) => {
      if (!result) return <p>Looking up {args.modelId}…</p>;
      return (
        <SpecSheet
          title={result.title}
          subtitle={result.subtitle}
          rows={result.rows}
          visibleCount={result.rows.length}
        />
      );
    },
  },
});

```

## Standalone (no runtime)

Standalone, the element is a plain display component: it owns no state of its own, so the minimal usage is a constant set of rows shown all at once.

```tsx
"use client";

import {
  SpecSheet,
  type SpecRow,
} from "@/components/assistant-ui/elements/spec-sheet";

const rows: readonly SpecRow[] = [
  { label: "context", value: "1,000,000 tokens" },
  { label: "input", value: "$4.00 / M" },
  { label: "best for", value: "Long agentic runs", emphasis: true },
];

export function ModelCard() {
  return (
    <SpecSheet
      title="Opus 5.5"
      subtitle="claude-opus-5-5"
      rows={rows}
      visibleCount={rows.length}
    />
  );
}

```
