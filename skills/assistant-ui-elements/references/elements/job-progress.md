# Job progress  (`job-progress`)

Weighted job stages that announce live progress and settle into a receipt.

- 完整文件：`references/pages/job-progress.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-job-progress.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-job-progress.md`

## With a runtime

This element has no assistant-ui primitive of its own, so render it from a tool. The loading branch starts from the stage information available to the renderer. When the tool resolves, pass its final status as `outcome` so the card becomes a receipt rather than inferring success from a stage count.

```tsx
"use client";

import { defineToolkit } from "@assistant-ui/react";
import { JobProgress } from "@/components/assistant-ui/elements/job-progress";

export const toolkit = defineToolkit({
  run_ci_job: {
    type: "backend",
    render: ({ args, result, addResult }) => {
      if (!result) {
        return (
          <JobProgress
            title={args.target}
            stages={args.stages ?? []}
            stageIndex={0}
            stageProgress={0}
            eta="starting"
            onCancel={() =>
              addResult({
                stageIndex: 0,
                stageProgress: 0,
                eta: "cancelled",
                outcome: {
                  status: "cancelled",
                  summary: "Cancelled before CI started.",
                },
                elapsedMs: 0,
              })
            }
          />
        );
      }

      return (
        <JobProgress
          title={args.target}
          stages={args.stages ?? []}
          stageIndex={result.stageIndex}
          stageProgress={result.stageProgress}
          eta={result.eta}
          outcome={result.outcome}
          elapsedMs={result.elapsedMs}
        />
      );
    },
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, the element is controlled. Your job runner owns the stage update and decides the final receipt.

```tsx
"use client";

import {
  JobProgress,
  type JobStage,
} from "@/components/assistant-ui/elements/job-progress";

const stages: readonly JobStage[] = [
  { name: "clone", weight: 1, description: "Fetching the branch" },
  { name: "install", weight: 4, description: "Restoring dependencies" },
  { name: "build", weight: 3, description: "Compiling packages" },
  { name: "test", weight: 2, description: "Running the suite" },
];

export function CiJob() {
  return (
    <JobProgress
      title="Verify the fix on CI"
      stages={stages}
      stageIndex={1}
      stageProgress={0.6}
      eta="about 3 min"
    />
  );
}

```
