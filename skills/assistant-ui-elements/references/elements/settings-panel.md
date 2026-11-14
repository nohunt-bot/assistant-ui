# Settings  (`settings-panel`)

Model, system prompt, temperature, and what the assistant is allowed to do.

- 完整文件：`references/pages/settings-panel.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-settings-panel.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-settings-panel.md`

## With a runtime

None of the panel's values live in the runtime by default; you hold them as component state and register them as a model context provider, the same mechanism `useAssistantInstructions` and the model selector use internally. Every request after that carries whatever the panel currently holds.

```tsx
"use client";

import { useEffect, useState } from "react";
import { useAui } from "@assistant-ui/react";
import {
  SettingsPanel,
  type SettingToggle,
} from "@/components/assistant-ui/elements/settings-panel";

const MODELS = ["gpt-5.6-luna", "gpt-5.6-terra"];

export function ModelSettings() {
  const aui = useAui();
  const [model, setModel] = useState(MODELS[0]!);
  const [systemPrompt, setSystemPrompt] = useState("You are a concise assistant.");
  const [temperature, setTemperature] = useState(0.7);
  const [webSearch, setWebSearch] = useState(false);

  useEffect(() => {
    return aui.modelContext.register({
      getModelContext: () => ({
        system: systemPrompt,
        callSettings: { temperature },
        config: { modelName: model },
      }),
    });
  }, [aui, model, systemPrompt, temperature]);

  const toggles: SettingToggle[] = [
    {
      key: "web-search",
      label: "Web search",
      detail: "Look things up before answering",
      on: webSearch,
    },
  ];

  return (
    <SettingsPanel
      model={model}
      models={MODELS}
      systemPrompt={systemPrompt}
      temperature={temperature}
      toggles={toggles}
      onModelChange={setModel}
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, every value is a prop and every change is a callback; the panel computes nothing beyond clamping temperature into range for display.

```tsx
"use client";

import { useState } from "react";
import {
  SettingsPanel,
  type SettingToggle,
} from "@/components/assistant-ui/elements/settings-panel";

export function Settings() {
  const [model, setModel] = useState("balanced");
  const [systemPrompt, setSystemPrompt] = useState("You are a helpful assistant.");
  const [temperature, setTemperature] = useState(1);
  const [toggles, setToggles] = useState<SettingToggle[]>([
    { key: "code", label: "Code execution", detail: "Run snippets in a sandbox", on: true },
    { key: "memory", label: "Memory", detail: "Remember facts across chats", on: false },
  ]);

  return (
    <SettingsPanel
      model={model}
      models={["fast", "balanced", "careful"]}
      systemPrompt={systemPrompt}
      temperature={temperature}
      toggles={toggles}
      onModelChange={setModel}
      onSystemPromptChange={setSystemPrompt}
      onTemperatureChange={setTemperature}
      onToggle={(key) =>
        setToggles((prev) => prev.map((t) => (t.key === key ? { ...t, on: !t.on } : t)))
      }
    />
  );
}

```
