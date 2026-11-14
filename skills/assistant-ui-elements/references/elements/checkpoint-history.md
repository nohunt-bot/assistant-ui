# Checkpoints  (`checkpoint-history`)

Points you can fall back to, with what each one would give back.

- 完整文件：`references/pages/checkpoint-history.md`（API reference、Anatomy、更多範例）
- 形式：standalone
- 安裝（standalone）：`source/<flavor>/aui/elements-checkpoint-history.md`

## Standalone (no runtime)

import { CheckpointHistory, type Checkpoint } from "@/components/assistant-ui/elements/checkpoint-history";

```tsx
"use client";

import { CheckpointHistory, type Checkpoint } from "@/components/assistant-ui/elements/checkpoint-history";

const checkpoints: Checkpoint[] = [
  { id: "1", label: "Initial scaffold", at: "10:02", files: 4 },
  { id: "2", label: "Added auth", at: "10:19", files: 7 },
  { id: "3", label: "Fixed layout bug", at: "10:41", files: 2 },
];

export function History() {
  return <CheckpointHistory checkpoints={checkpoints} currentId="3" />;
}

```
