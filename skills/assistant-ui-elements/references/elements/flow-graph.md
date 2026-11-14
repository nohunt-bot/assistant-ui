# Flow graph  (`flow-graph`)

Work as a graph rather than a list: branches that fan out and rejoin.

- 完整文件：`references/pages/flow-graph.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-flow-graph.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-flow-graph.md`

## With a runtime

With a runtime, a flow graph is usually one tool call whose arguments describe an entire plan: a list of steps, each naming what it depends on. A small layout function turns that list into the columns, rows, and edges `FlowGraph` expects.

```tsx
"use client";

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import {
  FlowGraph,
  type FlowEdge,
  type FlowNode,
} from "@/components/assistant-ui/elements/flow-graph";

type PlanStep = { id: string; label: string; dependsOn?: string[] };
type PlanArgs = { steps: PlanStep[] };

function layout(steps: PlanStep[], done: boolean) {
  const columnOf = new Map<string, number>();
  const rowsUsed = new Map<number, number>();
  const nodes: FlowNode[] = steps.map((step, index) => {
    const deps = step.dependsOn ?? [];
    const column = deps.length
      ? Math.max(...deps.map((id) => columnOf.get(id) ?? 0)) + 1
      : 0;
    columnOf.set(step.id, column);
    const row = rowsUsed.get(column) ?? 0;
    rowsUsed.set(column, row + 1);
    const state = done || index < steps.length - 1 ? "done" : "active";
    return { id: step.id, label: step.label, column, row, state };
  });
  const edges: FlowEdge[] = steps.flatMap((step) =>
    (step.dependsOn ?? []).map((from) => ({ from, to: step.id })),
  );
  return { nodes, edges };
}

export const PlanGraphToolUI: ToolCallMessagePartComponent<
  PlanArgs,
  unknown
> = ({ args, status }) => {
  const steps = args.steps ?? [];
  if (steps.length === 0) return null;
  const { nodes, edges } = layout(steps, status.type === "complete");
  return <FlowGraph nodes={nodes} edges={edges} visibleCount={nodes.length} />;
};

```

## Standalone (no runtime)

Standalone, you already hold `nodes` and `edges` with their layout decided; `visibleCount` is the one thing that changes over time.

```tsx
"use client";

import { useState } from "react";
import {
  FlowGraph,
  type FlowEdge,
  type FlowNode,
} from "@/components/assistant-ui/elements/flow-graph";

const NODES: readonly FlowNode[] = [
  { id: "intake", label: "intake", column: 0, row: 1, state: "done" },
  { id: "plan", label: "plan", column: 1, row: 1, state: "done" },
  { id: "search", label: "search", column: 2, row: 0, state: "done" },
  { id: "patch", label: "patch", column: 2, row: 2, state: "active" },
  { id: "verify", label: "verify", column: 3, row: 1, state: "pending" },
];

const EDGES: readonly FlowEdge[] = [
  { from: "intake", to: "plan" },
  { from: "plan", to: "search" },
  { from: "plan", to: "patch" },
  { from: "search", to: "verify" },
  { from: "patch", to: "verify" },
];

export function Plan() {
  const [visibleCount, setVisibleCount] = useState(NODES.length);
  return <FlowGraph nodes={NODES} edges={EDGES} visibleCount={visibleCount} />;
}

```
