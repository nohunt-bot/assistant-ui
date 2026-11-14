# Reviewable diff  (`reviewable-diff`)

The same diff, but each hunk is a decision: keep it, discard it, apply what survived.

- 完整文件：`references/pages/reviewable-diff.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-reviewable-diff.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-reviewable-diff.md`

## With a runtime

A patch review has no automatic outcome, so it registers as a human tool: there is no `execute`, and the call only resolves once the renderer calls `addResult`.

```tsx
"use client";

import { useState } from "react";
import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import {
  ReviewableDiff,
  type DiffHunk,
  type HunkDecision,
} from "@/components/assistant-ui/elements/reviewable-diff";

type ReviewPatchArgs = {
  filename: string;
  hunks: readonly Omit<DiffHunk, "decision">[];
};
type ReviewPatchResult = { kept: readonly string[] };

export const ReviewPatchToolUI: ToolCallMessagePartComponent<
  ReviewPatchArgs,
  ReviewPatchResult
> = ({ args, status, result, addResult }) => {
  const [decisions, setDecisions] = useState<Record<string, HunkDecision>>({});

  if (status.type === "running" || status.type === "incomplete") return null;

  if (result) {
    return (
      <p>
        Applied {result.kept.length} of {args.hunks.length} hunks to {args.filename}.
      </p>
    );
  }

  const hunks: DiffHunk[] = args.hunks.map((hunk) => ({
    ...hunk,
    decision: decisions[hunk.id] ?? "pending",
  }));

  return (
    <ReviewableDiff
      filename={args.filename}
      hunks={hunks}
      onKeep={(id) => setDecisions((d) => ({ ...d, [id]: "kept" }))}
      onDiscard={(id) => setDecisions((d) => ({ ...d, [id]: "discarded" }))}
      onApply={() =>
        addResult({
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, you hold the hunks and their decisions, and `onApply` runs whatever you choose once every hunk has a decision.

```tsx
"use client";

import { useState } from "react";
import {
  ReviewableDiff,
  type DiffHunk,
  type HunkDecision,
} from "@/components/assistant-ui/elements/reviewable-diff";

const HUNKS: readonly Omit<DiffHunk, "decision">[] = [
  {
    id: "h1",
    range: "@@ -12,6 +12,7",
    lines: [
      { kind: "context", text: "  const composer = useComposer();" },
      { kind: "removed", text: '  const [draft, setDraft] = useState("");' },
      { kind: "added", text: "  const draft = useDraft(threadId);" },
    ],
  },
];

export function PatchReview() {
  const [decisions, setDecisions] = useState<Record<string, HunkDecision>>({});
  const hunks: DiffHunk[] = HUNKS.map((hunk) => ({
    ...hunk,
    decision: decisions[hunk.id] ?? "pending",
  }));

  return (
    <ReviewableDiff
      filename="composer.tsx"
      hunks={hunks}
      onKeep={(id) => setDecisions((d) => ({ ...d, [id]: "kept" }))}
      onDiscard={(id) => setDecisions((d) => ({ ...d, [id]: "discarded" }))}
      onApply={() => applyPatch(hunks.filter((h) => h.decision === "kept"))}
    />
  );
}

```
