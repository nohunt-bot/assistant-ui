# Cost meter  (`cost-meter`)

What the run spent, split by model, against the session total.

- 完整文件：`references/pages/cost-meter.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-cost-meter.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-cost-meter.md`

## With a runtime

assistant-ui tracks token usage per step of a run, but not dollars and not which model produced a given step. Both are your own bookkeeping. The closest data is `message.metadata.steps`, an array of `{ usage: { inputTokens, outputTokens } }` entries.

```tsx
"use client";

import { useAuiState } from "@assistant-ui/react";
import {
  CostMeter,
  type CostLine,
} from "@/components/assistant-ui/elements/cost-meter";

const PRICE_PER_MILLION = { in: 3, out: 15 };

function useRunCost(model: string): CostLine {
  const inputTokens = useAuiState((s) =>
    s.message.role === "assistant"
      ? s.message.metadata.steps.reduce(
          (total, step) => total + (step.usage?.inputTokens ?? 0),
          0,
        )
      : 0,
  );
  const outputTokens = useAuiState((s) =>
    s.message.role === "assistant"
      ? s.message.metadata.steps.reduce(
          (total, step) => total + (step.usage?.outputTokens ?? 0),
          0,
        )
      : 0,
  );
  const dollars =
    (inputTokens / 1_000_000) * PRICE_PER_MILLION.in +
    (outputTokens / 1_000_000) * PRICE_PER_MILLION.out;
  return { model, inputTokens, outputTokens, cost: `$${dollars.toFixed(2)}`, share: 1 };
}

export function RunCost({ model }: { model: string }) {
  const line = useRunCost(model);
  return <CostMeter runCost={line.cost} sessionCost={line.cost} lines={[line]} />;
}

```

## Standalone (no runtime)

Standalone, you already have the numbers, from wherever your backend reports them.

```tsx
"use client";

import {
  CostMeter,
  type CostLine,
} from "@/components/assistant-ui/elements/cost-meter";

const LINES: CostLine[] = [
  { model: "Opus 5", inputTokens: 48_200, outputTokens: 12_400, cost: "$1.66", share: 0.62 },
  { model: "Sonnet 5", inputTokens: 92_800, outputTokens: 21_100, cost: "$0.86", share: 0.29 },
  { model: "Haiku 4.5", inputTokens: 140_000, outputTokens: 8_900, cost: "$0.24", share: 0.09 },
];

export function Usage() {
  return <CostMeter runCost="$2.76" sessionCost="$18.40" lines={LINES} />;
}

```
