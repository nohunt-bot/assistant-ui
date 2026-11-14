# Map  (`map-answer`)

A location answer: pins, a route between them, and the list they came from.

- 完整文件：`references/pages/map-answer.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-map-answer.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-map-answer.md`

## With a runtime

assistant-ui has no map or geocoding concept, and MapAnswer draws no real tiles or projection: `x` and `y` are plain 0 to 100 percentages that place a pin on the grid, not latitude and longitude. Whatever looks places up is responsible for turning real coordinates into that percentage layout before the result reaches this component.

```tsx
"use client";

import { useState } from "react";
import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import {
  MapAnswer,
  type MapPin,
} from "@/components/assistant-ui/elements/map-answer";

type FindPlacesResult = {
  pins: MapPin[];
  route: boolean;
};

export const FindPlacesToolUI: ToolCallMessagePartComponent<
  { query: string },
  FindPlacesResult
> = ({ result }) => {
  const [activeId, setActiveId] = useState("");

  if (!result) return null;
  const activePin = activeId || (result.pins[0]?.id ?? "");

  return (
    <MapAnswer
      pins={result.pins}
      activeId={activePin}
      route={result.route}
      onSelect={setActiveId}
    />
  );
};

```

## Standalone (no runtime)

Standalone, you already hold the pins, their layout percentages, and which one is active.

```tsx
"use client";

import { useState } from "react";
import {
  MapAnswer,
  type MapPin,
} from "@/components/assistant-ui/elements/map-answer";

const PINS: MapPin[] = [
  { id: "a", label: "Ferry Building", detail: "0.4 mi", x: 28, y: 62 },
  { id: "b", label: "Coit Tower", detail: "1.1 mi", x: 55, y: 20 },
  { id: "c", label: "Chinatown", detail: "0.8 mi", x: 40, y: 45 },
];

export function Places() {
  const [activeId, setActiveId] = useState(PINS[0].id);
  return (
    <MapAnswer pins={PINS} activeId={activeId} route onSelect={setActiveId} />
  );
}

```
