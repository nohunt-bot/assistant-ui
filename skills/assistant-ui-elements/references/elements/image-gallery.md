# Image gallery  (`image-gallery`)

A compact image grid that opens every result in one shared lightbox.

- 完整文件：`references/pages/image-gallery.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-image-gallery.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-image-gallery.md`

## With a runtime

A backend tool can return its finished images as `{ images }`. Its render function passes that result straight to `ImageGallery`, so a later tool result creates a separate gallery in the transcript.

```tsx
"use client";

import {
  defineToolkit,
  type ToolCallMessagePartComponent,
} from "@assistant-ui/react";
import {
  ImageGallery,
  type GalleryImage,
} from "@/components/assistant-ui/elements/image-gallery";

type ImageSearchResult = { images: GalleryImage[] };

const ImageSearchToolUI: ToolCallMessagePartComponent<
  { query: string },
  ImageSearchResult
> = ({ result }) => <ImageGallery images={result?.images ?? []} />;

export const toolkit = defineToolkit({
  show_image_results: {
    type: "backend",
    render: ImageSearchToolUI,
  },
});

```

## Standalone (no runtime)

Render the gallery with images from your own search or generation request. `onOpen` is useful for lightweight analytics or for synchronizing a nearby detail panel.

```tsx
import { ImageGallery, type GalleryImage } from "@/components/assistant-ui/elements/image-gallery";

const images: GalleryImage[] = [
  {
    id: "redwood",
    src: "/results/redwood.jpg",
    alt: "Sunlight through a redwood grove",
    caption: "Generated landscape, variation 1",
  },
];

export function Results() {
  return <ImageGallery images={images} onOpen={(id) => console.log(id)} />;
}
```
