# Model selector  (`model-selector`)

A searchable runtime model picker with grouped providers and reasoning effort controls.

- 完整文件：`references/pages/model-selector.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/model-selector.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-model-selector.md`

## With a runtime

Each model needs an `id` and a display `name`; everything else is optional.

```tsx
import { ModelSelector } from "@/components/assistant-ui/elements/model-selector.aui";

function ComposerAction() {
  return (
    <ModelSelector
      models={[
        { id: "gpt-6-luna", name: "GPT-6 Luna", description: "Fast and efficient" },
        { id: "gpt-6-sol", name: "GPT-6 Sol", description: "Balanced performance" },
        { id: "gpt-6-astra", name: "GPT-6 Astra", description: "Most capable", efforts: true },
      ]}
      defaultValue="gpt-6-luna"
      defaultEffort="medium"
      size="sm"
    />
  );
}

```

## Standalone (no runtime)

Standalone, the same pieces render with no registration: you own the value, the effort, and what happens when they change.

```tsx
"use client";

import { useState } from "react";
import {
  ModelSelectorRoot,
  ModelSelectorTrigger,
  ModelSelectorContent,
  type ModelOption,
} from "@/components/assistant-ui/elements/model-selector";

function SelectableModel() {
  const models: ModelOption[] = [
    { id: "gpt-6-luna", name: "GPT-6 Luna" },
    { id: "gpt-6-astra", name: "GPT-6 Astra", efforts: true },
  ];
  const [value, setValue] = useState("gpt-6-astra");
  const [effort, setEffort] = useState<string>("high");

  return (
    <ModelSelectorRoot
      models={models}
      value={value}
      onValueChange={setValue}
      effort={effort}
      onEffortChange={setEffort}
    >
      <ModelSelectorTrigger />
      <ModelSelectorContent />
    </ModelSelectorRoot>
  );
}

```
