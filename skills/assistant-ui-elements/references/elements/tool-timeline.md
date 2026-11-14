# Tool timeline  (`tool-timeline`)

A whole working session summarized as verbs, targets, and file stats.

- 完整文件：`references/pages/tool-timeline.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-tool-timeline.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-tool-timeline.md`

## With a runtime

A single tool call renders through that tool's own `render` field, but a timeline summarizes every call in the message at once, so it reads `s.message.parts` directly instead of registering as one tool's renderer.

```tsx
"use client";

import { useMemo, useState } from "react";
import {
  FileSearchIcon,
  PenLineIcon,
  TerminalIcon,
  type LucideIcon,
} from "lucide-react";
import { useAuiState, type ToolCallMessagePart } from "@assistant-ui/react";
import {
  ToolTimeline,
  type TimelineStat,
  type TimelineStep,
} from "@/components/assistant-ui/elements/tool-timeline";

const TOOL_META: Record<string, { verb: string; icon: LucideIcon }> = {
  read_file: { verb: "Read", icon: FileSearchIcon },
  run_command: { verb: "Ran", icon: TerminalIcon },
  edit_file: { verb: "Edited", icon: PenLineIcon },
};

function toStep(part: ToolCallMessagePart): TimelineStep {
  const meta = TOOL_META[part.toolName];
  const args = part.args as Record<string, unknown>;
  return {
    verb: meta?.verb ?? part.toolName,
    chip: String(args.path ?? args.command ?? part.toolCallId),
    icon: meta?.icon ?? TerminalIcon,
  };
}

function toStats(parts: readonly ToolCallMessagePart[]): TimelineStat[] {
  return parts
    .filter((part) => part.toolName === "edit_file" && part.result)
    .map((part) => {
      const result = part.result as { file: string; added: number; removed: number };
      return { file: result.file, added: result.added, removed: result.removed };
    });
}

export function SessionTimeline() {
  const [open, setOpen] = useState(false);
  const parts = useAuiState((s) => s.message.parts);
  const toolCalls = useMemo(
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, you hold the step and stat arrays as state and grow them as work happens.

```tsx
"use client";

import { useState } from "react";
import { FileSearchIcon, PenLineIcon, TerminalIcon } from "lucide-react";
import {
  ToolTimeline,
  type TimelineStat,
  type TimelineStep,
} from "@/components/assistant-ui/elements/tool-timeline";

const STEPS: TimelineStep[] = [
  { verb: "Read", chip: "thread.tsx", icon: FileSearchIcon },
  { verb: "Ran", chip: "pnpm vitest", icon: TerminalIcon },
  { verb: "Edited", chip: "composer.tsx", icon: PenLineIcon },
];

const STATS: TimelineStat[] = [{ file: "composer.tsx", added: 14, removed: 3 }];

export function Session() {
  const [open, setOpen] = useState(false);

  return (
    <ToolTimeline
      steps={STEPS}
      visibleSteps={STEPS.length}
      streaming={false}
      open={open}
      onOpenChange={setOpen}
      restingLabel="3 steps · 1 file changed"
      activeLabel="Working"
      stats={STATS}
    />
  );
}

```
