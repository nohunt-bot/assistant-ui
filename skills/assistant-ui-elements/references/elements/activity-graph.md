# Activity graph  (`activity-graph`)

A half-year of runs as a calendar of cells, dense where the work was.

- 完整文件：`references/pages/activity-graph.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-activity-graph.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-activity-graph.md`

## With a runtime

With a runtime, `data` is exactly the shape a tool would return: one count per day over some window. Register a tool that reports it and the graph needs nothing else.

```tsx
"use client";

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import type { DataPoint } from "heat-graph";
import { ActivityGraph } from "@/components/assistant-ui/elements/activity-graph";

type ActivityArgs = { days: number };
type ActivityResult = {
  title: string;
  total: string;
  start: string;
  end: string;
  data: DataPoint[];
};

export const RunActivityToolUI: ToolCallMessagePartComponent<
  ActivityArgs,
  ActivityResult
> = ({ result }) => {
  if (!result) return null;
  return (
    <ActivityGraph
      data={result.data}
      start={result.start}
      end={result.end}
      title={result.title}
      total={result.total}
    />
  );
};

```

## Standalone (no runtime)

Standalone, hold the same `data`, `start`, and `end` yourself; the element does the grid layout, the tinting, and the legend.

```tsx
"use client";

import type { DataPoint } from "heat-graph";
import { ActivityGraph } from "@/components/assistant-ui/elements/activity-graph";

const START = new Date(2026, 1, 2);
const DAYS = 182;
const dayAfterStart = (offset: number) =>
  new Date(START.getFullYear(), START.getMonth(), START.getDate() + offset);
const END = dayAfterStart(DAYS - 1);

const DATA: readonly DataPoint[] = Array.from({ length: DAYS }, (_, i) => ({
  date: dayAfterStart(i),
  count: Math.round(Math.random() * 12),
}));

const TOTAL = DATA.reduce((sum, point) => sum + point.count, 0);

export function AgentActivity() {
  return (
    <ActivityGraph
      data={DATA}
      start={START}
      end={END}
      title="Agent runs"
      total={`${TOTAL.toLocaleString("en-US")} in 6 months`}
    />
  );
}

```
