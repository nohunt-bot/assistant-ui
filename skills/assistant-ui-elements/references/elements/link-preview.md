# Link preview  (`link-preview`)

A compact card that gives a returned URL enough context to decide whether to open it.

- 完整文件：`references/pages/link-preview.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-link-preview.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-link-preview.md`

## With a runtime

A backend tool can return the URL metadata it resolved. Its renderer waits for that result, then hands the fields directly to `LinkPreview`.

```tsx
import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import { LinkPreview } from "@/components/assistant-ui/elements/link-preview";

type PreviewLinkResult = {
  href: string;
  title?: string;
  description?: string;
  image?: string;
  siteName?: string;
};

export const PreviewLinkToolUI: ToolCallMessagePartComponent<
  Record<string, never>,
  PreviewLinkResult
> = ({ result }) => (result ? <LinkPreview {...result} /> : null);

```

## Standalone (no runtime)

Pass resolved metadata from your own fetcher or CMS. `href` is required, and the rest of the card is optional.

```tsx
import { LinkPreview } from "@/components/assistant-ui/elements/link-preview";

export function RelatedLink() {
  return (
    <LinkPreview
      href="https://react.dev/learn/you-might-not-need-an-effect"
      title="You might not need an Effect"
      description="Derive values during render before reaching for an Effect."
      siteName="React"
    />
  );
}

```
