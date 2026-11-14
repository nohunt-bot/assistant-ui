# ActionBarMorePrimitive
URL: /docs/api-reference/primitives/action-bar-more

Overflow menu primitives for grouping secondary assistant message actions in a custom React UI.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## Anatomy

```
import { ActionBarPrimitive, ActionBarMorePrimitive } from "@assistant-ui/react";

const MessageActions = () => (
  <ActionBarPrimitive.Root>
    <ActionBarPrimitive.Copy />
    <ActionBarPrimitive.Reload />

    <ActionBarMorePrimitive.Root>
      <ActionBarMorePrimitive.Trigger>
        <MoreHorizontalIcon />
      </ActionBarMorePrimitive.Trigger>
      <ActionBarMorePrimitive.Content>
        <ActionBarMorePrimitive.Item onSelect={() => console.log("Edit")}>
          Edit
        </ActionBarMorePrimitive.Item>
        <ActionBarMorePrimitive.Item onSelect={() => console.log("Speak")}>
          Read aloud
        </ActionBarMorePrimitive.Item>
        <ActionBarMorePrimitive.Separator />
        <ActionBarMorePrimitive.Item onSelect={() => console.log("Feedback")}>
          Submit feedback
        </ActionBarMorePrimitive.Item>
      </ActionBarMorePrimitive.Content>
    </ActionBarMorePrimitive.Root>
  </ActionBarPrimitive.Root>
);
```

## API Reference

### Root

- `dir?`: `Direction | undefined`
- `open?`: `boolean | undefined`
- `defaultOpen?`: `boolean | undefined`
- `onOpenChange?`: `{ bivarianceHack(open: boolean): void; }['bivarianceHack'] | undefined`
- `modal?`: `boolean | undefined`

### Trigger

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

| Data attribute    | Values                |
| ----------------- | --------------------- |
| `[data-state]`    | `"open" \| "closed"`  |
| `[data-disabled]` | Present when disabled |

### Content

This primitive renders a `<div>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.

- `side?`: `Side | undefined`

- `sideOffset?`: `number | undefined`

- `align?`: `Align | undefined`

- `alignOffset?`: `number | undefined`

- `arrowPadding?`: `number | undefined`

- `avoidCollisions?`: `boolean | undefined`

- `collisionBoundary?`: `Boundary | Boundary[] | undefined`

- `collisionPadding?`: `number | Partial<Record<Side, number>> | undefined`

- `sticky?`: `Sticky | undefined`

- `hideWhenDetached?`: `boolean | undefined`

- `updatePositionStrategy?`: `UpdatePositionStrategy | undefined`

- `onCloseAutoFocus?`: `FocusScopeProps['onUnmountAutoFocus'] | undefined` — Event handler called when auto-focusing on close. Can be prevented.

- `loop?`: `RovingFocusGroupProps['loop'] | undefined` — Whether keyboard navigation should loop around

- `onEscapeKeyDown?`: `DismissableLayerProps['onEscapeKeyDown'] | undefined`

- `onPointerDownOutside?`: `DismissableLayerProps['onPointerDownOutside'] | undefined`

- `onFocusOutside?`: `DismissableLayerProps['onFocusOutside'] | undefined`

- `onInteractOutside?`: `DismissableLayerProps['onInteractOutside'] | undefined`

- `forceMount?`: `true | undefined` — Used to force mounting when more control is needed. Useful when controlling animation with React animation libraries.

- `render?`: `ReactElement | undefined`

- `portalProps?`: `ComponentPropsWithoutRef<typeof DropdownMenuPrimitive.Portal> | undefined`

  - `container?`: `PortalProps['container'] | undefined` — Specify a container element to portal the content into.
  - `forceMount?`: `true | undefined` — Used to force mounting when more control is needed. Useful when controlling animation with React animation libraries.

| Data attribute | Values                                   |
| -------------- | ---------------------------------------- |
| `[data-state]` | `"open" \| "closed"`                     |
| `[data-side]`  | `"top" \| "right" \| "bottom" \| "left"` |
| `[data-align]` | `"start" \| "center" \| "end"`           |

### Item

This primitive renders a `<div>` element unless `asChild` is set.

- `disabled?`: `boolean | undefined`
- `onSelect?`: `{ bivarianceHack(event: Event): void; }['bivarianceHack'] | undefined`
- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `textValue?`: `string | undefined`
- `render?`: `ReactElement | undefined`

| Data attribute       | Values                   |
| -------------------- | ------------------------ |
| `[data-disabled]`    | Present when disabled    |
| `[data-highlighted]` | Present when highlighted |

### Separator

This primitive renders a `<div>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`