# Image generation  (`image-generation`)

A dot grid holds the frame while the image resolves out of a blur.

- 完整文件：`references/pages/image-generation.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-image-generation.md`、`source/<flavor>/aui/thread.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-image-generation.md`

## With a runtime

An image tool renders through that tool's own toolkit entry: the model's prompt arrives as `args`, and the call's lifecycle as `status`.

```tsx
"use generative";

import { defineToolkit, externalTool } from "@assistant-ui/react";
import { ImageGeneration } from "@/components/assistant-ui/elements/image-generation";

export default defineToolkit({
  generate_image: {
    execute: externalTool(),
    render: ({ args, status }) => (
      <ImageGeneration
        prompt={args.prompt ?? ""}
        generating={status.type === "running"}
      />
    ),
  },
});

```

## Standalone (no runtime)

Standalone, `ImageGeneration` is fully controlled: you hold the prompt and whether generation is in flight, and optionally handle regeneration.

```tsx
"use client";

import { useCallback, useEffect, useState } from "react";
import { ImageGeneration } from "@/components/assistant-ui/elements/image-generation";

export function Generation() {
  const prompt = "A calm mountain lake at dawn";
  const [generating, setGenerating] = useState(true);

  return (
    <ImageGeneration prompt={prompt} generating={generating} />
  );
}

```
