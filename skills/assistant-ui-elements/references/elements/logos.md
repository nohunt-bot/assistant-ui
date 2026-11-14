# Model logos  (`logos`)

Inline SVG marks for OpenAI, Anthropic, and Google model providers.

- 完整文件：`references/pages/logos.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-model-selector.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔

## With a runtime

`ModelSelector`'s own `ModelOption` type carries an optional `icon: ReactNode`, rendered next to the model's name in both the trigger and the list. assistant-ui doesn't map marks to providers; set the icon when you build the option list.

```tsx
import { ClaudeLogo, OpenAILogo, GeminiLogo } from "@/components/assistant-ui/elements/logos";
import type { ModelOption } from "@/components/assistant-ui/elements/model-selector";

export const models: ModelOption[] = [
  { id: "claude-sonnet-5", name: "Sonnet 5", icon: <ClaudeLogo className="size-4" /> },
  { id: "gpt-6-luna", name: "GPT-6", icon: <OpenAILogo className="size-4" /> },
  { id: "gemini-3.1-pro", name: "Gemini 3.1 Pro", icon: <GeminiLogo className="size-4" /> },
];

```

## Standalone (no runtime)

Standalone, there's no state at all: render the mark you need, sized and colored like any other icon.

```tsx
import { ClaudeLogo, OpenAILogo, GeminiLogo } from "@/components/assistant-ui/elements/logos";

export function Providers() {
  return (
    <div className="flex items-center gap-3">
      <OpenAILogo className="size-6" />
      <ClaudeLogo className="size-6" />
      <GeminiLogo className="size-6" />
    </div>
  );
}

```
