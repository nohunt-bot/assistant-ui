# Heat graph  (`heat-graph`)

An activity heat map with month labels, weekday labels, legend, and tooltip.

- 完整文件：`references/pages/heat-graph.md`（API reference、Anatomy、更多範例）
- 形式：runtime
- 安裝（runtime）：`source/<flavor>/aui/heat-graph.md`

## With a runtime

const data = [
     { date: "2026-01-01", count: 3 },
     { date: "2026-01-02", count: 0 },
     { date: new Date(2026, 0, 3), count: 7 },
   ];

```tsx
import { HeatGraph } from "@/components/assistant-ui/elements/heat-graph";

const data = [
  { date: "2026-01-01", count: 3 },
  { date: "2026-01-02", count: 0 },
  { date: new Date(2026, 0, 3), count: 7 },
];

export function Activity() {
  return <HeatGraph data={data} />;
}

```
