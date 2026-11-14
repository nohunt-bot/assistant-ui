# Diagram  (`diagram`)

A drawn answer with zoom, reset, and a full-bleed view; you hand it the rendered graphic.

- 完整文件：`references/pages/diagram.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-diagram.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-diagram.md`

## With a runtime

With a runtime, `Diagram` renders whatever a tool has already turned into a graphic: an image URL, output from a charting library, or markup from a renderer like Mermaid. It owns only the zoom chrome; the zoom level itself stays with the caller, since every zoom callback is optional and the current `zoom` is a plain prop.

```tsx
"use client";

import { useState } from "react";
import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import { Diagram } from "@/components/assistant-ui/elements/diagram";

type RenderDiagramResult = { title: string; imageUrl: string };

export const RenderDiagramToolUI: ToolCallMessagePartComponent<
  { prompt: string },
  RenderDiagramResult
> = ({ result }) => {
  const [zoom, setZoom] = useState(1);
  if (!result) return null;

  return (
    <Diagram
      title={result.title}
      zoom={zoom}
      onZoomIn={() => setZoom((z) => Math.min(2, z + 0.2))}
      onZoomOut={() => setZoom((z) => Math.max(0.4, z - 0.2))}
      onReset={() => setZoom(1)}
    >
      <img src={result.imageUrl} alt={result.title} className="max-w-none" />
    </Diagram>
  );
};

```

## Standalone (no runtime)

Standalone, `Diagram` never touches `zoom` itself: hold it in state and clamp it yourself, the way the handlers below do.

```tsx
"use client";

import { useState } from "react";
import { Diagram } from "@/components/assistant-ui/elements/diagram";
import { FlowSvg } from "./flow-svg";

export function MessageFlow() {
  const [zoom, setZoom] = useState(1);

  return (
    <Diagram
      title="message flow"
      zoom={zoom}
      onZoomIn={() => setZoom((z) => Math.min(1.6, z + 0.2))}
      onZoomOut={() => setZoom((z) => Math.max(0.6, z - 0.2))}
      onReset={() => setZoom(1)}
    >
      <FlowSvg />
    </Diagram>
  );
}

```
