# Geo map  (`geo-map`)

Real coordinates become a tiled map, route lines, and an accessible list of the places returned by a tool.

- 完整文件：`references/pages/geo-map.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-geo-map.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-geo-map.md`

## With a runtime

A backend place search returns `{ places, routes }`, then the tool renderer passes those coordinates directly to GeoMap. The map is ready once the result arrives, while the rest of the message can explain why these places matter.

```tsx
"use client";

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import {
  GeoMap,
  type GeoMapPlace,
  type GeoMapRoute,
} from "@/components/assistant-ui/elements/geo-map";

type FindPlacesResult = {
  places: GeoMapPlace[];
  routes: GeoMapRoute[];
};

export const FindPlacesToolUI: ToolCallMessagePartComponent<
  { query: string },
  FindPlacesResult
> = ({ result }) => {
  if (!result) return null;
  return <GeoMap places={result.places} routes={result.routes} />;
};

```

## Standalone (no runtime)

Standalone, pass places and route points from any data source. Without `selectedId`, GeoMap selects its first valid place and maintains selection itself.

```tsx
import { GeoMap } from "@/components/assistant-ui/elements/geo-map";

export function Nearby({ places, routes }) {
  return <GeoMap places={places} routes={routes} />;
}

```
