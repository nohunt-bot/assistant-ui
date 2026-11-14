# Research report  (`research-report`)

An outline that fills in section by section, each carrying the sources behind it.

- 完整文件：`references/pages/research-report.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-research-report.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-research-report.md`

## With a runtime

assistant-ui streams a tool call's arguments as partial JSON while the model writes them, so the report can be the argument itself and render before the call finishes. Ask the model for the report as structured `args` and read them live.

```tsx
"use client";

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import {
  ResearchReport,
  type ReportSection,
} from "@/components/assistant-ui/elements/research-report";

type WriteReportArgs = {
  title: string;
  sections: ReportSection[];
  sourcesRead: number;
};

export const WriteReportToolUI: ToolCallMessagePartComponent<
  WriteReportArgs,
  void
> = ({ args }) => (
  <ResearchReport
    title={args.title ?? "Untitled report"}
    sections={args.sections ?? []}
    sourcesRead={args.sourcesRead ?? 0}
  />
);

```

## Standalone (no runtime)

Standalone, you own the section list end to end, including every state transition.

```tsx
"use client";

import { useState } from "react";
import {
  ResearchReport,
  type ReportSection,
} from "@/components/assistant-ui/elements/research-report";

const INITIAL: ReportSection[] = [
  {
    id: "intro",
    heading: "Why this matters",
    state: "done",
    sources: 3,
    preview: "Three independent benchmarks agree on the direction.",
  },
  { id: "method", heading: "How it was measured", state: "writing", sources: 1 },
  { id: "results", heading: "Results", state: "pending", sources: 0 },
];

export function Report() {
  const [sections, setSections] = useState(INITIAL);
  return (
    <ResearchReport title="Adoption trends" sections={sections} sourcesRead={7} />
  );
}

```
