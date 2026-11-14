# Model selector
URL: /elements/model-selector

A searchable runtime model picker with grouped providers and reasoning effort controls.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

Model selector is a popover that lists AI models and, for models that support it, a reasoning effort level: Low, Medium, High, or a custom set. With a runtime the selection registers with assistant-ui's `ModelContext` system automatically, so it reaches your backend on every request; standalone you hold the value and effort yourself. It comes in two designs: the runtime variant renders a popover of models with reasoning-effort controls, and the static variant, `ModelPicker`, renders a full-page list grouped by family with pricing and capability chips (see [The model-picker design](#the-model-picker-design)).

## Getting started

**With a runtime:**

1. ### Add the picker

   Each model needs an `id` and a display `name`; everything else is optional.

   ```
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

2. ### Read the selection in your API route

   The selected model's `id` arrives as `config.modelName`, and its effort level, when the model supports one, as `config.reasoningEffort`.

   ```
   export async function POST(req: Request) {
     const { messages, config } = await req.json();

     const result = streamText({
       abortSignal: req.signal,
       model: openai(config?.modelName ?? "gpt-6-luna"),
       providerOptions: {
         openai:
           config?.reasoningEffort !== undefined
             ? { reasoningEffort: config.reasoningEffort }
             : {},
       },
       messages: await convertToModelMessages(messages),
     });

     return result.toUIMessageStreamResponse();
   }
   ```

**Standalone (no runtime):**

Standalone, the same pieces render with no registration: you own the value, the effort, and what happens when they change.

1. ### Hold the value yourself

   \[interactive preview component SelectableModel omitted]

   Code for SelectableModel preview:

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

   Open the picker and select a model: the trigger shows the new model once it closes, and the Thinking row follows whichever model declares `efforts`.

## Anatomy

```
<button role="combobox" aria-haspopup="listbox" data-slot="model-selector-trigger" />

<div data-slot="model-selector-content"> {/* a Command (cmdk) list */}
  <input data-slot="model-selector-search" /> {/* only when searchable */}
  <div data-slot="model-selector-list">
    <div data-slot="model-selector-empty" /> {/* only when search has no matches */}
    <div data-slot="model-selector-group">
      <div data-slot="model-selector-item" /> {/* one per model */}
    </div>
  </div>
  <div data-slot="model-selector-effort" /> {/* only when the selected model declares efforts */}
</div>
```

The Thinking row (`Effort`) mounts only while the selected model has `efforts` set. Switching to a model without it hides the row but keeps your effort choice, and switching back to a model that offers that level restores it. `Content` needs a focused input to drive the keyboard. With `searchable={false}`, or with no custom `children` and `searchable` unset, it renders a visually hidden `FocusAnchor`. A custom layout that has neither `Search` nor `searchable={false}` gets no anchor, so add `FocusAnchor` yourself to keep it keyboard-operable.

## Examples

### Reasoning efforts

A model that declares `efforts` shows the Thinking row. `efforts: true` enables the default Low / Medium / High levels; a list of `{ id, name }` objects defines a custom set instead, and omitting `efforts` hides the row entirely for that model.

Open the picker and pick a level: the trigger shows it next to the model name, and the picker stays open so you can compare levels.

\[interactive preview component ModelSelectorWithEffort omitted]

Code for ModelSelectorWithEffort preview:

```tsx
"use client";

import { useState } from "react";
import {
  ModelSelectorRoot,
  ModelSelectorTrigger,
  ModelSelectorContent,
  type ModelOption,
} from "@/components/assistant-ui/elements/model-selector";

function ModelSelectorWithEffort() {
  const models: ModelOption[] = [
    {
      id: "gpt-6-astra",
      name: "GPT-6 Astra",
      efforts: [
        { id: "minimal", name: "Minimal" },
        { id: "standard", name: "Standard" },
        { id: "extended", name: "Extended" },
      ],
    },
  ];
  const [model, setModel] = useState("gpt-6-astra");
  const [effort, setEffort] = useState("standard");

  return (
    <ModelSelectorRoot
      models={models}
      value={model}
      onValueChange={setModel}
      effort={effort}
      onEffortChange={setEffort}
    >
      <ModelSelectorTrigger className="min-w-[204px]" />
      <ModelSelectorContent />
    </ModelSelectorRoot>
  );
}
```

### Custom effort UI

`Effort`'s default layout is horizontal segments, which overflows once a model offers more than a few levels. `useModelSelectorEfforts` exposes the selected model's levels and the active selection for any other layout, from inside `Content`:

```
import { useModelSelectorEfforts } from "@/components/assistant-ui/elements/model-selector";
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuRadioGroup,
  DropdownMenuRadioItem,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";

function EffortDropdown() {
  const { efforts, effort, setEffort } = useModelSelectorEfforts();
  if (!efforts?.length) return null;

  return (
    <DropdownMenu>
      <DropdownMenuTrigger>{efforts.find((e) => e.id === effort)?.name ?? "Select"}</DropdownMenuTrigger>
      <DropdownMenuContent align="end">
        <DropdownMenuRadioGroup value={effort} onValueChange={setEffort}>
          {efforts.map((option) => (
            <DropdownMenuRadioItem key={option.id} value={option.id}>
              {option.name}
            </DropdownMenuRadioItem>
          ))}
        </DropdownMenuRadioGroup>
      </DropdownMenuContent>
    </DropdownMenu>
  );
}
```

Render it inside `Content` in place of `Effort`.

### Provider logos

Each model's `icon` accepts any `ReactNode` and renders in both the trigger and the dropdown item. [Model logos](/elements/logos) ships ready-made `OpenAILogo`, `ClaudeLogo`, and `GeminiLogo` marks for this.

```
{ id: "claude-opus-5-5", name: "Claude Opus 5.5", icon: <ClaudeLogo /> }
```

### Capability metadata

`ModelSelectorItem` accepts custom `children` in place of the default name and description row. This demo uses that to render capability badges.

Open the picker to compare model capabilities. Select a row to update the trigger.

\[interactive preview component ModelSelectorWithMetadata omitted]

Code for ModelSelectorWithMetadata preview:

```tsx
"use client";

import { useState } from "react";
import {
  ModelSelectorRoot,
  ModelSelectorTrigger,
  ModelSelectorContent,
  ModelSelectorList,
  ModelSelectorItem,
  type ModelOption,
} from "@/components/assistant-ui/elements/model-selector";
import { Badge } from "@/components/ui/badge";
import { cn } from "@/lib/utils";

function ModelSelectorWithMetadata() {
  const models: ModelOption[] = [
    { id: "gpt-6-luna", name: "GPT-6 Luna" },
    { id: "gpt-6-astra", name: "GPT-6 Astra" },
    { id: "claude-opus-5-5", name: "Claude Opus 5.5" },
    { id: "gemini-3.8-flash", name: "Gemini 3.8 Flash" },
  ];
  const capabilities: Record<string, string[]> = {
    "gpt-6-luna": ["Tools", "1M"],
    "gpt-6-astra": ["Vision", "Tools", "1M"],
    "claude-opus-5-5": ["Vision", "Tools", "1M"],
    "gemini-3.8-flash": ["Vision", "Tools", "1M"],
  };
  const [model, setModel] = useState("gpt-6-astra");

  return (
    <ModelSelectorRoot models={models} value={model} onValueChange={setModel}>
      <ModelSelectorTrigger />
      <ModelSelectorContent searchable={false}>
        <ModelSelectorList>
          {models.map((option, index) => (
            <ModelSelectorItem
              key={option.id}
              model={option}
              className={cn(
                "rounded-none",
                index === 0 && "rounded-t-lg",
                index === models.length - 1 && "rounded-b-lg",
              )}
            >
              <span className="flex min-w-0 flex-col gap-1">
                <span className="truncate font-medium">{option.name}</span>
                <span className="flex gap-1">
                  {capabilities[option.id]?.map((capability) => (
                    <Badge key={capability} variant="secondary">
                      {capability}
                    </Badge>
                  ))}
                </span>
              </span>
            </ModelSelectorItem>
          ))}
        </ModelSelectorList>
      </ModelSelectorContent>
    </ModelSelectorRoot>
  );
}
```

### Search

Search is opt-in: pass `searchable` to the connected `ModelSelector`, or to `ModelSelectorContent` directly when it renders its default children. Matching runs against each model's `id`, `name`, and `keywords`; add a provider name to `keywords` so typing it finds that provider's models.

```
<ModelSelector models={models} searchable />
{/* or, standalone: */}
<ModelSelectorContent searchable />
```

Open the picker and search for a model or provider. Select a result to update the trigger.

\[interactive preview component SearchableModelSelector omitted]

Code for SearchableModelSelector preview:

```tsx
"use client";

import { useState } from "react";
import {
  ModelSelectorRoot,
  ModelSelectorTrigger,
  ModelSelectorContent,
  ModelSelectorSearch,
  ModelSelectorList,
  ModelSelectorEmpty,
  ModelSelectorGroup,
  ModelSelectorItem,
  type ModelOption,
} from "@/components/assistant-ui/elements/model-selector";
import {
  ClaudeLogo,
  GeminiLogo,
  OpenAILogo,
} from "@/components/assistant-ui/elements/logos";

function SearchableModelSelector() {
  const models: ModelOption[] = [
    {
      id: "gpt-6-luna",
      name: "GPT-6 Luna",
      keywords: ["OpenAI"],
      icon: <OpenAILogo />,
    },
    {
      id: "gpt-6-astra",
      name: "GPT-6 Astra",
      keywords: ["OpenAI"],
      icon: <OpenAILogo />,
    },
    {
      id: "claude-fable-5-1",
      name: "Claude Fable 5.1",
      keywords: ["Anthropic"],
      icon: <ClaudeLogo />,
    },
    {
      id: "claude-opus-5-5",
      name: "Claude Opus 5.5",
      keywords: ["Anthropic"],
      icon: <ClaudeLogo />,
    },
    {
      id: "gemini-3.8-flash",
      name: "Gemini 3.8 Flash",
      keywords: ["Google"],
      icon: <GeminiLogo />,
    },
  ];
  const [model, setModel] = useState("gpt-6-luna");

  return (
    <ModelSelectorRoot models={models} value={model} onValueChange={setModel}>
      <ModelSelectorTrigger />
      <ModelSelectorContent>
        <ModelSelectorSearch />
        <ModelSelectorList>
          <ModelSelectorEmpty>No matching models.</ModelSelectorEmpty>
          <ModelSelectorGroup>
            {models.map((option) => (
              <ModelSelectorItem key={option.id} model={option} />
            ))}
          </ModelSelectorGroup>
        </ModelSelectorList>
      </ModelSelectorContent>
    </ModelSelectorRoot>
  );
}
```

### Provider groups and availability

Use `ModelSelectorGroup` to group the models by provider. A model with `disabled` shows in the list, but you cannot select it. In this example, Claude Opus 5.5 is disabled, so clicking it does not change the selection.

\[interactive preview component ModelAvailabilitySelector omitted]

Code for ModelAvailabilitySelector preview:

```tsx
"use client";

import { useState } from "react";
import {
  ModelSelectorRoot,
  ModelSelectorTrigger,
  ModelSelectorContent,
  ModelSelectorList,
  ModelSelectorGroup,
  ModelSelectorItem,
  ModelSelectorEffort,
  type ModelOption,
} from "@/components/assistant-ui/elements/model-selector";

function ModelAvailabilitySelector() {
  const openaiModels: ModelOption[] = [
    { id: "gpt-6-luna", name: "GPT-6 Luna" },
    { id: "gpt-6-astra", name: "GPT-6 Astra", efforts: true },
  ];
  const anthropicModels: ModelOption[] = [
    { id: "claude-fable-5-1", name: "Claude Fable 5.1" },
    { id: "claude-opus-5-5", name: "Claude Opus 5.5", disabled: true },
  ];
  const [model, setModel] = useState("gpt-6-luna");

  return (
    <ModelSelectorRoot
      models={[...openaiModels, ...anthropicModels]}
      value={model}
      onValueChange={setModel}
    >
      <ModelSelectorTrigger />
      <ModelSelectorContent searchable={false}>
        <ModelSelectorList>
          <ModelSelectorGroup heading="OpenAI">
            {openaiModels.map((option) => (
              <ModelSelectorItem key={option.id} model={option} />
            ))}
          </ModelSelectorGroup>
          <ModelSelectorGroup heading="Anthropic">
            {anthropicModels.map((option) => (
              <ModelSelectorItem key={option.id} model={option} />
            ))}
          </ModelSelectorGroup>
        </ModelSelectorList>
        <ModelSelectorEffort />
      </ModelSelectorContent>
    </ModelSelectorRoot>
  );
}
```

### Variants and sizes

`variant` and `size` live on the trigger: pass them to the connected `ModelSelector`, or to `ModelSelectorTrigger` directly in a standalone layout.

```
<ModelSelector variant="ghost" size="lg" />
{/* or, standalone: */}
<ModelSelectorTrigger variant="ghost" size="lg" />
```

| Variant   | Description                              |
| --------- | ---------------------------------------- |
| `outline` | Border, transparent background (default) |
| `ghost`   | No background                            |
| `muted`   | Solid secondary background               |

| Size      | Description     |
| --------- | --------------- |
| `sm`      | Compact, `h-8`  |
| `default` | Standard, `h-9` |
| `lg`      | Larger, `h-10`  |

## API reference

**With a runtime:**

### ModelSelector

| Prop                             | Type                              | Default     | Description                                  |
| -------------------------------- | --------------------------------- | ----------- | -------------------------------------------- |
| `models`                         | `ModelOption[]`                   | required    | Models to display.                           |
| `value` / `defaultValue`         | `string`                          | first model | Controlled or initial selected model id.     |
| `onValueChange`                  | `(value: string) => void`         |             | Called when the selection changes.           |
| `effort` / `defaultEffort`       | `string`                          |             | Controlled or initial effort level id.       |
| `onEffortChange`                 | `(effort: string) => void`        |             | Called when the effort level changes.        |
| `searchable`                     | `boolean`                         | `false`     | Renders a search input above the model list. |
| `variant`                        | `"outline" \| "ghost" \| "muted"` | `"outline"` | Trigger style.                               |
| `size`                           | `"sm" \| "default" \| "lg"`       | `"default"` | Trigger size.                                |
| `align`                          | `"start" \| "center" \| "end"`    | `"start"`   | Popover alignment relative to the trigger.   |
| `className` / `contentClassName` | `string`                          |             | Merged onto the trigger / popover content.   |

### How registration works

1. `ModelSelector` calls `aui.modelContext.register()` with `config.modelName` set to the selected id, plus `config.reasoningEffort` whenever the selected model supports the chosen level.
2. `AssistantChatTransport` (from `@assistant-ui/ai-sdk`) includes `config` in the body of every chat request.
3. Your API route reads `config.modelName` and `config.reasoningEffort`.

`ModelSelector.Root` (the same `ModelSelectorRoot` documented below) performs no registration on its own; it is purely presentational.

**Standalone (no runtime):**

### ModelSelectorRoot and sub-components

| Export                     | Renders                 | Notes                                                                                                   |
| -------------------------- | ----------------------- | ------------------------------------------------------------------------------------------------------- |
| `ModelSelectorRoot`        | provider + Popover      | Holds `models` and controlled/uncontrolled value, effort, and open state. No DOM of its own.            |
| `ModelSelectorTrigger`     | `button`                | `role="combobox"`; `ArrowDown`/`ArrowUp` open the popover when focused.                                 |
| `ModelSelectorValue`       | `span`                  | Selected model's icon, name, and (if `showEffort`) effort name; a placeholder when nothing is selected. |
| `ModelSelectorContent`     | popover + `Command`     | Wraps a cmdk `Command`; `searchable` and `side` are the notable props.                                  |
| `ModelSelectorSearch`      | `input` (cmdk)          | Filters `List` as you type.                                                                             |
| `ModelSelectorFocusAnchor` | visually hidden `input` | Anchors keyboard navigation when there is no visible search box.                                        |
| `ModelSelectorList`        | cmdk list               | Renders all models grouped under one `Group` by default.                                                |
| `ModelSelectorEmpty`       | cmdk empty state        | Shown when a search has no matches.                                                                     |
| `ModelSelectorGroup`       | cmdk group              | A labeled group of items, e.g. one per provider.                                                        |
| `ModelSelectorSeparator`   | cmdk separator          | Divider between groups or items.                                                                        |
| `ModelSelectorItem`        | cmdk item               | One model; takes a `model: ModelOption` prop.                                                           |
| `ModelSelectorEffort`      | `div` + radiogroup      | The Thinking row; renders `null` when the selected model has no `efforts`.                              |

### ModelOption

| Field         | Type                                        | Description                                                                                                       |
| ------------- | ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `id`          | `string`                                    | Sent to the backend as `modelName` by the connected `ModelSelector`; not read by `ModelSelectorRoot` itself.      |
| `name`        | `string`                                    | Shown in the trigger and the item.                                                                                |
| `description` | `string`                                    | Optional subtitle under the name in the item.                                                                     |
| `icon`        | `ReactNode`                                 | Optional, shown before the name.                                                                                  |
| `disabled`    | `boolean`                                   | Disables selecting this model.                                                                                    |
| `keywords`    | `string[]`                                  | Extra terms `ModelSelectorSearch` matches, beyond `id` and `name`.                                                |
| `efforts`     | `boolean \| { id: string; name: string }[]` | `true` for the default Low/Medium/High; a list for a custom set; omitted to hide the Thinking row for this model. |

### Effort helpers

| Export                                        | Type                                               | Description                                                                                                                                 |
| --------------------------------------------- | -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `useModelSelectorEfforts()`                   | `() => { efforts, effort, setEffort }`             | The selected model's effort levels and the active selection; must be called inside `ModelSelectorRoot`.                                     |
| `resolveModelEffort(models, modelId, effort)` | `(models, modelId, effort) => string \| undefined` | Returns `effort` when the given model supports it, otherwise `undefined`; the sticky-selection rule `ModelSelectorRoot` applies internally. |
| `useModelSelectorContext()`                   | `() => ModelSelectorContextValue`                  | The raw context sub-components read from; throws outside `ModelSelectorRoot`. Not re-exported from the `.aui` path.                         |

## The model-picker design

The Static variant in the rail is a second design for the same picker: `ModelPicker` renders every model in a full-page list grouped by family, each row carrying its context window, price, and capability chips, with the active model checked, instead of a searchable popover. It is a single props-driven component with no runtime dependency:

```
npx shadcn@latest add "@assistant-ui/elements-model-picker"
```

**With a runtime:**

With a runtime there is no catalog of available models to read: assistant-ui tracks the model configuration for the next call, not a directory of every model you could switch to. Unlike the popover, `ModelPicker` does no registration on its own, so register a provider that reports the selected id and seed the initial selection from whatever the model context already reports, for example a default set on the server.

```
"use client";

import { useAui, useAuiState } from "@assistant-ui/react";
import { useEffect, useState } from "react";
import {
  ModelPicker,
  type PickableModel,
} from "@/components/assistant-ui/elements/model-picker";

export function ModelPickerPanel({
  models,
}: {
  models: readonly PickableModel[];
}) {
  const aui = useAui();
  const activeModelName = useAuiState((s) => s.modelContext.modelName);
  const [selectedId, setSelectedId] = useState(
    () => models.find((model) => model.id === activeModelName)?.id ?? models[0]!.id,
  );

  useEffect(() => {
    return aui.modelContext.register({
      getModelContext: () => ({ config: { modelName: selectedId } }),
    });
  }, [aui, selectedId]);

  return (
    <ModelPicker models={models} selectedId={selectedId} onSelect={setSelectedId} />
  );
}
```

**Standalone (no runtime):**

Standalone, `ModelPicker` is a plain controlled list: it groups `models` by family, keeping each family's first-seen order, and reports a pick through `onSelect`.

```
"use client";

import { useState } from "react";
import {
  ModelPicker,
  type PickableModel,
} from "@/components/assistant-ui/elements/model-picker";

const MODELS: PickableModel[] = [
  { id: "sonnet", name: "Claude Sonnet 5", family: "Anthropic", context: "1M", price: "$2 / 1M", capabilities: ["vision", "tools"] },
  { id: "gpt-6-sol", name: "GPT-6 Sol", family: "OpenAI", context: "1M", price: "$2 / 1M", capabilities: ["tools", "thinking"] },
];

export function ModelPickerPanel() {
  const [selectedId, setSelectedId] = useState(MODELS[0]!.id);
  return (
    <ModelPicker models={MODELS} selectedId={selectedId} onSelect={setSelectedId} />
  );
}
```

Families are derived from `models` with `[...new Set(models.map((m) => m.family))]`, so a family's position is set by the first model that names it, not alphabetically. Each row is a plain toggle button (`aria-pressed`, not a listbox option), so there is no arrow-key navigation built in; Tab moves between rows the same as any button list. The check mark's column keeps its width whether or not the row is selected, so rows never shift when the selection changes.

### ModelPicker

| Prop         | Type                       | Default  | Description                                                                                                                                                                                                         |
| ------------ | -------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `models`     | `readonly PickableModel[]` | required | `{ id, name, family, context, price, capabilities }[]`. Families keep first-seen order; `context` and `price` are pre-formatted strings (the element does no currency math), `capabilities` renders as chip labels. |
| `selectedId` | `string`                   | required | Which model is active.                                                                                                                                                                                              |
| `onSelect`   | `(id: string) => void`     |          | Called when a model is picked. Without it, model rows render as non-interactive `div`s.                                                                                                                             |
| `className`  | `string`                   |          | Merged onto the root.                                                                                                                                                                                               |