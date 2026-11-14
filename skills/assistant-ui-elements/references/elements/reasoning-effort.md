# Reasoning effort  (`reasoning-effort`)

How hard to think, and how much of that budget the run actually spent.

- 完整文件：`references/pages/reasoning-effort.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-reasoning-effort.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-reasoning-effort.md`

## With a runtime

`reasoningEffort` is a field on assistant-ui's model context, which the [model selector](/elements/model-selector) also uses to pass an effort setting to the model. Selecting a level and reading how much it cost are separate: the first is a registration, the second is usage data from the adapter.

```tsx
"use client";

import { useEffect, useState } from "react";
import { useAui } from "@assistant-ui/react";
import { ReasoningEffort, type EffortLevel } from "@/components/assistant-ui/elements/reasoning-effort";

const LEVELS: readonly EffortLevel[] = [
  { key: "low", label: "Low", budget: 2_000 },
  { key: "medium", label: "Medium", budget: 8_000 },
  { key: "high", label: "High", budget: 24_000 },
];

function useRegisteredEffort() {
  const [selectedKey, setSelectedKey] = useState("medium");
  const aui = useAui();

  useEffect(() => {
    return aui.modelContext.register({
      getModelContext: () => ({ config: { reasoningEffort: selectedKey } }),
    });
  }, [aui, selectedKey]);

  return { selectedKey, setSelectedKey };
}

```

## Standalone (no runtime)

Standalone, `levels`, `selectedKey`, and `spent` are all plain values you own; the element only renders them and reports clicks.

```tsx
"use client";

import { useState } from "react";
import { ReasoningEffort, type EffortLevel } from "@/components/assistant-ui/elements/reasoning-effort";

const LEVELS: readonly EffortLevel[] = [
  { key: "low", label: "Low", budget: 2_000 },
  { key: "high", label: "High", budget: 24_000 },
];

export function Effort() {
  const [selectedKey, setSelectedKey] = useState("low");
  return (
    <ReasoningEffort levels={LEVELS} selectedKey={selectedKey} spent={0} onSelect={setSelectedKey} />
  );
}

```
