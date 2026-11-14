# Computer use  (`computer-use`)

The screen the agent is driving, with a cursor trail and what it is doing right now.

- 完整文件：`references/pages/computer-use.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-computer-use.md`

## With a runtime

There's no built-in "computer use" tool. This is a `computer` tool you define yourself, one call per action, rendered together by grouping consecutive calls to it.

```tsx
"use client";

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import { defineToolkit } from "@assistant-ui/react";
import { MousePointer2Icon } from "lucide-react";
import { z } from "zod";

type ComputerArgs = { action: string; target: string; x: number; y: number };

const ComputerStepMarker: ToolCallMessagePartComponent<ComputerArgs> = ({ args, status }) => (
  <MousePointer2Icon
    className="absolute size-4 fill-blue-500 text-blue-500 transition-[left,top] duration-500"
    style={{ left: `${args.x}%`, top: `${args.y}%`, opacity: status?.type === "running" ? 1 : 0.4 }}
  />
);

const toolkit = defineToolkit({
  computer: {
    type: "backend",
    description: "Click, type, or scroll at a point on the screen.",
    parameters: z.object({ action: z.string(), target: z.string(), x: z.number(), y: z.number() }),
    render: ComputerStepMarker,
  },
});

```

## Standalone (no runtime)

Standalone, the element is a controlled component: you own the step list, which one is active, and the screen content underneath the trail.

```tsx
"use client";

import { useState } from "react";
import { ComputerUse, type ComputerStep } from "@/components/assistant-ui/elements/computer-use";

const steps: ComputerStep[] = [
  { id: "1", action: "click", target: "Sign in", x: 62, y: 24 },
  { id: "2", action: "type", target: "harry@example.com", x: 40, y: 38 },
];

export function Screen() {
  const [activeIndex, setActiveIndex] = useState(0);
  return (
    <ComputerUse url="app.example.com/login" steps={steps} activeIndex={activeIndex}>
      <img src="/screenshot.png" alt="" className="size-full object-cover" />
    </ComputerUse>
  );
}

```
