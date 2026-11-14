# ThreadListItemPrimitive
URL: /docs/api-reference/primitives/thread-list-item

Thread list item primitives for rendering selectable conversation rows with titles, archive controls, delete actions, and menus.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## Anatomy

```
import { ThreadListItemPrimitive } from "@assistant-ui/react";

const ThreadListItem = () => (
  <ThreadListItemPrimitive.Root>
    <ThreadListItemPrimitive.Trigger>
      <ThreadListItemPrimitive.Title />
    </ThreadListItemPrimitive.Trigger>
    <ThreadListItemPrimitive.Archive />
    <ThreadListItemPrimitive.Unarchive />
    <ThreadListItemPrimitive.Delete />
  </ThreadListItemPrimitive.Root>
);
```

## API Reference

### Root

This primitive renders a `<div>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

| Data attribute  | Values                                   |
| --------------- | ---------------------------------------- |
| `[data-active]` | Present when this is the current thread. |

### Archive

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Unarchive

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Delete

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Trigger

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Title

- `fallback?`: `ReactNode`