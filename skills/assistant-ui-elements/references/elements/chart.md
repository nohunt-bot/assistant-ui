# Chart  (`chart`)

Area, line, and bars, with points landing one at a time as the series streams in.

- 完整文件：`references/pages/chart.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-chart.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-chart.md`

## With a runtime

A chart's series usually comes from the same tool call that produces its headline value, so it renders through that tool's `render` field. Because `args` is a best-effort parse of the JSON the model is still writing, `args.points` holds however many elements have streamed in so far; pass its length straight through as `visibleCount` and the chart reveals points as they arrive.

```tsx
"use client";

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import { Chart } from "@/components/assistant-ui/elements/chart";

type PlotMetricArgs = { label: string; points: number[] };
type PlotMetricResult = { value: string; delta?: string };

export const PlotMetricToolUI: ToolCallMessagePartComponent<
  PlotMetricArgs,
  PlotMetricResult
> = ({ args, result }) => {
  const points = args.points ?? [];
  return (
    <Chart
      label={args.label ?? ""}
      value={result?.value ?? "…"}
      delta={result?.delta}
      points={points}
      visibleCount={points.length}
      variant="area"
    />
  );
};

```

## Standalone (no runtime)

Standalone, `points` is the full series and `visibleCount` is how much of it to draw. Grow `visibleCount` on a timer to replay a stream.

```tsx
"use client";

import { useState } from "react";
import { Chart } from "@/components/assistant-ui/elements/chart";

const POINTS = [18, 22, 19, 31, 28, 42, 38, 51, 47, 63, 58, 71, 69, 84, 92];

export function RunsChart() {
  const [visibleCount, setVisibleCount] = useState(POINTS.length);
  return (
    <Chart
      label="Runs this week"
      value="92"
      delta="+34%"
      points={POINTS}
      visibleCount={visibleCount}
      variant="area"
    />
  );
}

```
