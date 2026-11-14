# Generative UI  (`generative-ui`)

A styled component library for rendering structured generative UI output.

- 完整文件：`references/pages/generative-ui.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/thread.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔

## With a runtime

The library only supplies components; something still has to expose them to the model and mount the result when it calls them. `JSONGenerativeUI` builds that bridge: a tool whose parameters come from the library's Zod schemas, and whose render draws the model's response through the library.

```tsx
import { defineToolkit } from "@assistant-ui/react";
import { JSONGenerativeUI } from "@assistant-ui/generative-ui/react";
import { styledGenerativeUILibrary } from "@/components/assistant-ui/elements/generative-ui";

const generative = new JSONGenerativeUI({ library: styledGenerativeUILibrary });

export default defineToolkit({
  present: generative.present(),
});

```

## Standalone (no runtime)

Standalone, skip the tool: call `renderGenerativeUI` with any spec built against the library.

```tsx
import { renderGenerativeUI } from "@assistant-ui/generative-ui/react";
import { styledGenerativeUILibrary } from "@/components/assistant-ui/elements/generative-ui";

function Report() {
  return renderGenerativeUI(
    { $type: "Markdown", value: "**Revenue** is up 12% this quarter." },
    styledGenerativeUILibrary,
  );
}

```
