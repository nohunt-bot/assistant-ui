# Code diff  (`code-diff`)

A unified diff with tinted additions and removals, sized for chat.

- 完整文件：`references/pages/code-diff.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-code-diff.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-code-diff.md`

## With a runtime

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
   import { CodeDiff, type DiffLine } from "@/components/assistant-ui/elements/code-diff";

```tsx
"use client";

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import { CodeDiff, type DiffLine } from "@/components/assistant-ui/elements/code-diff";

type EditFileArgs = { path: string };
type EditFileResult = { additions: number; deletions: number; lines: DiffLine[] };

export const EditFileToolUI: ToolCallMessagePartComponent<
  EditFileArgs,
  EditFileResult
> = ({ args, result }) => {
  if (!result) return null;
  return (
    <CodeDiff
      filename={args.path}
      additions={result.additions}
      deletions={result.deletions}
      lines={result.lines}
      cycle={0}
    />
  );
};

```

## Standalone (no runtime)

import { CodeDiff, type DiffLine } from "@/components/assistant-ui/elements/code-diff";

```tsx
"use client";

import { CodeDiff, type DiffLine } from "@/components/assistant-ui/elements/code-diff";

const LINES: DiffLine[] = [
  { kind: "context", text: "export function Composer() {" },
  { kind: "removed", text: '  const [draft, setDraft] = useState("");' },
  { kind: "added", text: "  const draft = useDraft(threadId);" },
];

export function Patch() {
  return (
    <CodeDiff
      filename="composer.tsx"
      additions={1}
      deletions={1}
      lines={LINES}
      cycle={0}
    />
  );
}

```
