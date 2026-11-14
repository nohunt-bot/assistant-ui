# Terminal block  (`terminal-block`)

Command output that streams line by line and ends with an exit status.

- 完整文件：`references/pages/terminal-block.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-terminal-block.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-terminal-block.md`

## With a runtime

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
   import { TerminalBlock } from "@/components/assistant-ui/elements/terminal-block";

```tsx
"use client";

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import { TerminalBlock } from "@/components/assistant-ui/elements/terminal-block";

type RunCommandArgs = { command: string; cwd?: string };
type RunCommandResult = {
  stdout: string;
  stderr: string;
  exitCode: number;
  durationMs?: number;
  truncated?: boolean;
};

export const RunCommandToolUI: ToolCallMessagePartComponent<
  RunCommandArgs,
  RunCommandResult
> = ({ args, result, status }) => {
  const lines = result?.stdout ? result.stdout.split("\n") : [];
  const stderr = result?.stderr ? result.stderr.split("\n") : [];
  return (
    <TerminalBlock
      command={args.command ?? ""}
      lines={lines}
      stderr={stderr}
      cwd={args.cwd}
      exitCode={result?.exitCode}
      durationMs={result?.durationMs}
      truncated={result?.truncated}
      visibleCount={lines.length + stderr.length}
      maxCollapsedLines={12}
      done={status.type !== "running"}
    />
  );
};

```

## Standalone (no runtime)

import { TerminalBlock } from "@/components/assistant-ui/elements/terminal-block";

```tsx
"use client";

import { TerminalBlock } from "@/components/assistant-ui/elements/terminal-block";

const RESULT = {
  stdout: "RUN v4.0.5\n✓ 3 tests passed",
  stderr: "",
  exitCode: 0,
};

export function TestRun() {
  return (
    <TerminalBlock
      command="pnpm vitest run"
      lines={RESULT.stdout.split("\n")}
      stderr={RESULT.stderr ? RESULT.stderr.split("\n") : []}
      exitCode={RESULT.exitCode}
      visibleCount={2}
      done
    />
  );
}

```
