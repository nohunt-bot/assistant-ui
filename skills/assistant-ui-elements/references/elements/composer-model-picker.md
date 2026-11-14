# Models  (`composer-model-picker`)

The model lives in the composer rail, one tap away with context at a glance.

- 完整文件：`references/pages/composer-model-picker.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-composer.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-composer.md`

## With a runtime

There is no dedicated primitive for a model trigger, because "the active model" is not thread or message state the way branches or attachments are; it is whatever your app registers through the general-purpose model context system. Read the registered name to show it, and register a new one to change it.

```tsx
import { useAuiState } from "@assistant-ui/react";

const modelName = useAuiState((s) => s.modelContext.modelName);

```

## Standalone (no runtime)

Standalone, the trigger and menu are fully controlled: you hold which model is selected and whether the menu is open, and the two catalog pieces only render that state.

```tsx
"use client";

import { useState } from "react";
import { ComposerModelTrigger, ComposerMenu, ComposerModelItem, type ComposerModel } from "@/components/assistant-ui/elements/composer";

const models: ComposerModel[] = [
  { name: "Fast", meta: "$0.25/M" },
  { name: "Frontier", meta: "$3/M" },
];

export function ModelPicker() {
  const [selected, setSelected] = useState(models[0]);
  const [open, setOpen] = useState(false);

  return (
    <div className="relative">
      <ComposerModelTrigger model={selected.name} open={open} onClick={() => setOpen((v) => !v)} />
      <ComposerMenu open={open} align="end">
        {models.map((entry) => (
          <ComposerModelItem
            key={entry.name}
            entry={entry}
            selected={entry.name === selected.name}
            onClick={() => {
              setSelected(entry);
              setOpen(false);
            }}
          />
        ))}
      </ComposerMenu>
    </div>
  );
}

```
