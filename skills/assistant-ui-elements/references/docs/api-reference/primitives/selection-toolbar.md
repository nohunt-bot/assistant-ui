# SelectionToolbarPrimitive
URL: /docs/api-reference/primitives/selection-toolbar

Selection toolbar primitives for quote, copy, and contextual actions on selected chat text.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

For examples and usage patterns, see [SelectionToolbar](/docs/primitives/selection-toolbar).

## Anatomy

```
import { SelectionToolbarPrimitive } from "@assistant-ui/react";

const FloatingSelectionToolbar = () => (
  <SelectionToolbarPrimitive.Root>
    <SelectionToolbarPrimitive.Quote>Quote</SelectionToolbarPrimitive.Quote>
  </SelectionToolbarPrimitive.Root>
);
```

Place this component inside `ThreadPrimitive.Root`:

```
<ThreadPrimitive.Root>
  <ThreadPrimitive.Viewport>...</ThreadPrimitive.Viewport>
  <FloatingSelectionToolbar />
</ThreadPrimitive.Root>
```

To prevent tool cards or other message UI from being quoted, wrap the message text in `data-aui-quote-selectable`. If a message contains that marker, the toolbar only appears for selections inside the same marked region. Set the attribute to `"false"` to exclude an element instead; on the message root it disables quoting for the whole message.

If a selection crosses an excluded element, the toolbar does not appear; excluded text is never stripped from a quote. Selections that end immediately before or start immediately after the excluded element remain quotable. Because the whole selection is rejected, exclusions suit block-level UI such as tool cards and attachments; an exclusion placed inline in a paragraph makes any selection over that paragraph unquotable.

## API Reference

### Root

A floating toolbar that appears when text is selected within a message. Listens for browser selection changes, validates that the selection is within a single message, and renders a positioned portal near the selection. Prevents mousedown from clearing the selection.

This primitive renders a `<div>` element unless `asChild` is set.

```
<SelectionToolbarPrimitive.Root>
  <SelectionToolbarPrimitive.Quote>Quote</SelectionToolbarPrimitive.Quote>
</SelectionToolbarPrimitive.Root>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Quote

A button that quotes the currently selected text. Must be placed inside \`SelectionToolbarPrimitive.Root\`. Reads the selection info from context (captured by the Root), sets it as a quote in the thread composer, and clears the selection.

This primitive renders a `<button>` element unless `asChild` is set.

```
<SelectionToolbarPrimitive.Quote>
  <QuoteIcon /> Quote
</SelectionToolbarPrimitive.Quote>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`