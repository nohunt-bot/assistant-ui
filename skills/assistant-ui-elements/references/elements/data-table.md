# Data table  (`data-table`)

A compact, sortable table for structured tool results and application data.

- 完整文件：`references/pages/data-table.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-data-table.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-data-table.md`

## With a runtime

Use the same result shape your backend tool returns. The renderer stays small because the tool result already describes both the headings and values.

```tsx
"use client";

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import { DataTable, type DataTableColumn, type DataTableRow } from "@/components/assistant-ui/elements/data-table";

type CompareModelsResult = {
  columns: readonly DataTableColumn[];
  rows: readonly DataTableRow[];
};

export const CompareModelsToolUI: ToolCallMessagePartComponent<
  { models: string[] },
  CompareModelsResult
> = ({ result }) =>
  result ? (
    <DataTable
      columns={result.columns}
      rows={result.rows}
      rowKey="id"
      caption="Model comparison"
    />
  ) : null;

```

## Standalone (no runtime)

Pass the same payload yourself. `defaultSort` gives an initial order, and `sort` with `onSortChange` lets your application own that state.

```tsx
"use client";

import { useState } from "react";
import { DataTable, type DataTableColumn, type DataTableSort } from "@/components/assistant-ui/elements/data-table";

const columns = [
  { key: "name", label: "Model", priority: "primary" },
  { key: "context", label: "Context", format: { kind: "number", compact: true } },
  { key: "input", label: "Input", format: { kind: "currency", currency: "USD" } },
] as const satisfies readonly DataTableColumn[];

const rows = [
  { id: "sonnet", name: "Sonnet 4.5", context: 200_000, input: 3 },
  { id: "gpt", name: "GPT-5", context: 400_000, input: 5 },
];

export function ModelComparison() {
  const [sort, setSort] = useState<DataTableSort | null>({
    key: "context",
    direction: "desc",
  });

  return <DataTable columns={columns} rows={rows} rowKey="id" sort={sort} onSortChange={setSort} caption="Model comparison" />;
}
```
