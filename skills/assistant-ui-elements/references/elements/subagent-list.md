# Subagent list  (`subagent-list`)

Parallel workers with their own progress, models, and completions.

- 完整文件：`references/pages/subagent-list.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-subagent-list.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-subagent-list.md`

## With a runtime

A dispatch to several sub-agents is naturally several tool calls in one assistant turn, one per worker. The list itself is not any single call's UI; it is assembled from all of them.

```tsx
"use client";

import { defineToolkit } from "@assistant-ui/react";

export const toolkit = defineToolkit({
  dispatch_subagent: { type: "backend", render: () => null },
  summarize_findings: { type: "backend", render: () => null },
});

```

## Standalone (no runtime)

Standalone, the element is a controlled display: you own the roster and report each worker's progress into it as it changes.

```tsx
"use client";

import { useState } from "react";
import {
  SubagentList,
  type SubagentItem,
} from "@/components/assistant-ui/elements/subagent-list";

const AGENTS: readonly SubagentItem[] = [
  { name: "Explore the runtime", model: "haiku" },
  { name: "Fix composer types", model: "sonnet" },
  { name: "Write regression tests", model: "sonnet" },
];

export function Agents() {
  const [completedCount, setCompletedCount] = useState(0);
  const [progress, setProgress] = useState([0, 0, 0]);

  return (
    <SubagentList
      agents={AGENTS}
      completedCount={completedCount}
      progress={progress}
      showSummary={false}
      summaryAgent={{ name: "Summarize findings", model: "haiku" }}
    />
  );
}

```
