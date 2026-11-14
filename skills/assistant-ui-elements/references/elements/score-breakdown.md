# Score breakdown  (`score-breakdown`)

A verdict with its arithmetic shown: criteria, weights, and what pulled it down.

- 完整文件：`references/pages/score-breakdown.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-score-breakdown.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-score-breakdown.md`

## With a runtime

No assistant-ui primitive backs this element, so the runtime wiring is a tool renderer. A score usually comes from grading work, such as running tests or comparing against a rubric, so the criteria belong on the tool's `result`, not on its streamed `args`.

```tsx
"use client";

import { defineToolkit } from "@assistant-ui/react";
import { ScoreBreakdown } from "@/components/assistant-ui/elements/score-breakdown";

export const toolkit = defineToolkit({
  review_change: {
    type: "backend",
    render: ({ args, result }) => {
      if (!result) return <p>Reviewing {args.target}…</p>;
      return (
        <ScoreBreakdown
          verdict={result.verdict}
          total={result.total}
          outOf={result.outOf}
          criteria={result.criteria}
          visibleCount={result.criteria.length}
        />
      );
    },
  },
});

```

## Standalone (no runtime)

Standalone, the element is a display component with no state of its own. The minimal usage passes a constant set of criteria and shows them all at once.

```tsx
"use client";

import {
  ScoreBreakdown,
  type ScoreCriterion,
} from "@/components/assistant-ui/elements/score-breakdown";

const criteria: readonly ScoreCriterion[] = [
  {
    label: "Fixes the root cause",
    score: 4.5,
    weight: 3,
    note: "Clears the slot at the source rather than at the call site.",
  },
  { label: "Test coverage", score: 4, weight: 2 },
  { label: "Scope discipline", score: 3, weight: 1 },
];

export function ReviewVerdict() {
  return (
    <ScoreBreakdown
      verdict="approve"
      total={4.1}
      outOf={5}
      criteria={criteria}
      visibleCount={criteria.length}
    />
  );
}

```
