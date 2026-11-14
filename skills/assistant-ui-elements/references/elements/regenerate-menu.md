# Regenerate with  (`regenerate-menu`)

Fork the same turn to a different model instead of rolling the same dice.

- 完整文件：`references/pages/regenerate-menu.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-regenerate-menu.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-regenerate-menu.md`

## With a runtime

`aui.message.reload(config)` starts a new run for the current turn, and `config.runConfig.custom` reaches your model adapter untouched. assistant-ui has no built-in model field on a run. With `useLocalRuntime` and a `ChatModelAdapter`, `run(options)` receives the config as `options.runConfig`, so the adapter can read the pick for that run without changing what later turns use.

```tsx
"use client";

import { useState } from "react";
import { useAui } from "@assistant-ui/react";
import { RegenerateMenu, type RegenerateOption } from "@/components/assistant-ui/elements/regenerate-menu";

const MODELS: RegenerateOption[] = [
  { id: "opus", label: "Try again with Opus 5", detail: "slower" },
  { id: "sonnet", label: "Try again with Sonnet 5", detail: "balanced" },
  { id: "haiku", label: "Try again with Haiku 4.5", detail: "fastest" },
];

function RegenerateWith({ currentModel }: { currentModel: string }) {
  const aui = useAui();
  const [open, setOpen] = useState(false);

  return (
    <RegenerateMenu
      options={MODELS}
      open={open}
      currentId={currentModel}
      onOpenChange={setOpen}
      onPick={(id) => {
        aui.message.reload({ runConfig: { custom: { model: id } } });
        setOpen(false);
      }}
    />
  );
}

```

## Standalone (no runtime)

Standalone, the menu is fully controlled: open state, the current pick, and what happens on a pick are all yours.

```tsx
"use client";

import { useState } from "react";
import { RegenerateMenu, type RegenerateOption } from "@/components/assistant-ui/elements/regenerate-menu";

const OPTIONS: RegenerateOption[] = [
  { id: "opus", label: "Try again with Opus 5", detail: "slower" },
  { id: "sonnet", label: "Try again with Sonnet 5", detail: "balanced" },
];

export function Answer() {
  const [open, setOpen] = useState(false);
  const [model, setModel] = useState("sonnet");

  return (
    <RegenerateMenu
      options={OPTIONS}
      open={open}
      currentId={model}
      onOpenChange={setOpen}
      onPick={(id) => {
        setModel(id);
        setOpen(false);
        regenerate(id);
      }}
    />
  );
}

```
