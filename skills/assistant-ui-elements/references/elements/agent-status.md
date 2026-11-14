# Agent status  (`agent-status`)

One pill that always answers: what is it doing, and for how long.

- 完整文件：`references/pages/agent-status.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-agent-status.md`、`source/<flavor>/aui/agent-status.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-agent-status.md`

## With a runtime

import { AgentStatus } from "@/components/assistant-ui/elements/agent-status.aui";

```tsx
"use client";

import { AgentStatus } from "@/components/assistant-ui/elements/agent-status.aui";

export function HeaderStatus() {
  return <AgentStatus />;
}

```

## Standalone (no runtime)

Standalone, the element is a controlled display: you own `state`, `label`, and `elapsed`, and update them as the agent's work moves along.

```tsx
"use client";

import { useState } from "react";
import {
  AgentStatus,
  type AgentState,
} from "@/components/assistant-ui/elements/agent-status";

export function Status() {
  const [state, setState] = useState<AgentState>("working");
  const [label, setLabel] = useState("Refactoring composer");
  const [elapsed, setElapsed] = useState("0:04");

  return <AgentStatus state={state} label={label} elapsed={elapsed} />;
}

```
