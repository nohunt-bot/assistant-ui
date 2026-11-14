# Sources
URL: /elements/sources

Safe runtime citation chips and static source cards with linked reference details.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

Sources has two lanes. The runtime binding renders each message source as a compact chip. The static element collects citations behind one Sources pill and reveals linked cards with snippets and publication details.

## Getting started

**With a runtime:**

### Register the message source renderer

Register `Sources` as the `Source` renderer on `MessagePrimitive.Parts`. Every source part in the message routes through it automatically.

```
import { Sources } from "@/components/assistant-ui/elements/sources.aui";
import { MessagePrimitive } from "@assistant-ui/react";

function AssistantMessage() {
  return (
    <MessagePrimitive.Root>
      <MessagePrimitive.Parts components={{ Source: Sources }} />
    </MessagePrimitive.Root>
  );
}
```

A safe `url` part becomes a link, a `document` part becomes a badge, and any other shape renders nothing.

**Standalone (no runtime):**

### Render source cards

```
"use client";

import { useState } from "react";
import { Sources, type Source } from "@/components/assistant-ui/elements/sources";

const sources: Source[] = [
  {
    title: "You might not need an effect",
    url: "https://react.dev/learn/you-might-not-need-an-effect",
    snippet: "Derive values during render before reaching for an Effect.",
    author: "React Team",
    publishedAt: "2024-10-01",
  },
];

export function Citations() {
  const [open, setOpen] = useState(false);

  return (
    <Sources
      sources={sources}
      open={open}
      onOpenChange={setOpen}
    />
  );
}
```

The static element is controlled. `open` and `onOpenChange` belong to the caller. Set `layout="list"` for compact rows; safe links, snippets and publication details remain available in both layouts.

## Anatomy

**With a runtime:**

### Runtime source chip

```
{/* sourceType: "url" */}
<a data-slot="source" href={safeHref(url)}>
  <img data-slot="source-icon" />
  <span data-slot="source-title">{title ?? domain}</span>
</a>

{/* sourceType: "document" */}
<span data-slot="source">
  <span data-slot="source-document-icon" />
  <span data-slot="source-title">{title}</span>
</span>
```

The favicon requests `https://icons.duckduckgo.com/ip3/<domain>.ico` by default. When that image fails to load, the icon falls back to the domain initial. The visible title is `title` when present, otherwise the host with `www.` removed. A `document` source never links. An unsafe URL renders as plain text, never as an `href`.

**Standalone (no runtime):**

### Static source card

```
<a data-slot="source-card" href={safeHref(source.url)}>
  <span>{domain}</span>
  <span>{source.title}</span>
  <span>{source.snippet}</span>
  <span>{source.author} · {formatPublishedAt(source.publishedAt, locale)}</span>
</a>
```

The trigger begins with up to three overlapping domain initials, then its Sources label and full count. A card derives its domain from `url` when `domain` is omitted. Without a supplied or derived domain, the domain row is omitted. An unsafe URL renders as a plain text card.

## Examples

**With a runtime:**

### Custom favicon

Compose the exported sub-parts when the default favicon service is not appropriate. Keep the safe URL check before providing an `href`:

```
import { safeHref } from "@/components/assistant-ui/utils/href";
import { Source, SourceIcon, SourceTitle } from "@/components/assistant-ui/elements/sources.aui";

function CustomSource({ url, title }: { url: string; title: string }) {
  const href = safeHref(url);
  if (!href) return <span>{title}</span>;

  return (
    <Source href={href} variant="secondary">
      <SourceIcon
        url={url}
        faviconUrl={(domain) => `https://logo.clearbit.com/${domain}`}
      />
      <SourceTitle>{title}</SourceTitle>
    </Source>
  );
}
```

**Standalone (no runtime):**

### Linked reference details

Linked cards carry the excerpt a reader needs to judge a citation before opening it. `publishedAt` accepts an ISO date string and renders as a localized month and year.

```
const sources = [
  {
    title: "Runtime drafts API",
    url: "https://www.assistant-ui.com/docs/runtimes",
    snippet: "Keep a draft while the thread runtime streams and reconnects.",
    author: "assistant-ui",
    publishedAt: "2025-09-16",
  },
];
```

## API reference

**With a runtime:**

### Runtime bindings

| Export          | Renders                  | Notes                                                                                        |
| --------------- | ------------------------ | -------------------------------------------------------------------------------------------- |
| `Sources`       | `a`, text chip, or badge | The `Source` message-part renderer. Pass to `MessagePrimitive.Parts` as `components.Source`. |
| `Sources.Root`  | `a`                      | The link for a `url` source. Accepts `variant`, `size`, `href`, and other anchor props.      |
| `Sources.Icon`  | `img` or fallback `span` | Favicon for a `url` source. Accepts `url` and an optional `faviconUrl` resolver.             |
| `Sources.Title` | `span`                   | Truncated title or domain text, shared by both source shapes.                                |

### Source part

| Field        | Type                  | Description                                                                 |
| ------------ | --------------------- | --------------------------------------------------------------------------- |
| `sourceType` | `"url" \| "document"` | Which shape to render.                                                      |
| `id`         | `string`              | Source identifier.                                                          |
| `url`        | `string`              | Link target for `"url"`. Unsafe values render as text.                      |
| `title`      | `string`              | Optional for `"url"` (falls back to the domain); required for `"document"`. |
| `mediaType`  | `string`              | Required when `sourceType` is `"document"`. Not read by `Sources`.          |
| `filename`   | `string`              | Optional, `"document"` only. Not read by `Sources`.                         |

**Standalone (no runtime):**

### Static card props

| Prop           | Type                            | Description                                                                                               |
| -------------- | ------------------------------- | --------------------------------------------------------------------------------------------------------- |
| `sources`      | `readonly Source[]`             | Citation cards revealed by the pill.                                                                      |
| `open`         | `boolean`                       | Whether the source list is expanded.                                                                      |
| `onOpenChange` | `(open: boolean) => void`       | Receives the expanded state.                                                                              |
| `layout`       | `"grid" \| "list" \| undefined` | Two-column cards, or one compact row per source. Defaults to `"grid"`.                                    |
| `hideTrigger`  | `boolean \| undefined`          | Omits the pill when your own button drives `open`; give that button `aria-expanded`. Defaults to `false`. |
| `locale`       | `string \| undefined`           | Locale for `publishedAt` month and year. Defaults to `"en-US"`.                                           |
| `className`    | `string \| undefined`           | Extra classes merged onto the root.                                                                       |

`SourceGlyph`, exported next to `Sources`, renders a domain's initial the way the cards do, for a custom trigger that should match them. It takes a `domain: string`, fetches no icon, and is hidden from assistive technology, so pair it with a text label.

### Source fields

| Field         | Type                  | Description                                                       |
| ------------- | --------------------- | ----------------------------------------------------------------- |
| `title`       | `string`              | Card title.                                                       |
| `domain`      | `string \| undefined` | Optional visible domain. Falls back to the safe URL host.         |
| `url`         | `string \| undefined` | Optional link target. Unsafe values leave the card as plain text. |
| `snippet`     | `string \| undefined` | Optional two-line excerpt.                                        |
| `author`      | `string \| undefined` | Optional meta-line author.                                        |
| `publishedAt` | `string \| undefined` | Optional ISO date. Invalid dates are omitted.                     |