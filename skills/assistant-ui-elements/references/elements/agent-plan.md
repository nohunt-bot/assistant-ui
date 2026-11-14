# Agent plan  (`agent-plan`)

A checklist the agent works through, with progress you can glance.

- 完整文件：`references/pages/agent-plan.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-agent-plan.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-agent-plan.md`

## With a runtime

A plan like this is a natural shape for a tool call: the model calls a tool with the full step list and the index of the step it is currently on.

```tsx
"use client";

import { defineToolkit } from "@assistant-ui/react";
import { AgentPlan } from "@/components/assistant-ui/elements/agent-plan";

export const toolkit = defineToolkit({
  update_plan: {
    type: "backend",
    render: ({ args }) => (
      <AgentPlan
        title={args.title}
        steps={args.steps}
        activeIndex={args.activeIndex}
      />
    ),
  },
});

```

## Standalone (no runtime)

Standalone, the element is a controlled display: you own the step list and move `activeIndex` forward as work completes.

```tsx
"use client";

import { useState } from "react";
import { AgentPlan } from "@/components/assistant-ui/elements/agent-plan";

const STEPS = [
  { id: "read", label: "Read existing composer state" },
  {
    id: "design",
    label: "Design the draft store",
    description: "Keep pending changes local until they are ready.",
  },
  { id: "wire", label: "Wire runtime persistence" },
  { id: "test", label: "Add regression tests" },
  { id: "docs", label: "Update the docs" },
];

export function Plan() {
  const [activeIndex, setActiveIndex] = useState(0);
  return <AgentPlan title="Composer draft" steps={STEPS} activeIndex={activeIndex} />;
}

```
