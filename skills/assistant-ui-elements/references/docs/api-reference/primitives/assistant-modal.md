# AssistantModalPrimitive
URL: /docs/api-reference/primitives/assistant-modal

Floating assistant modal primitives for building support chat, copilot, and embedded assistant experiences.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

For examples and usage patterns, see [AssistantModal](/docs/primitives/assistant-modal).

## Anatomy

```
import { AssistantModalPrimitive } from "@assistant-ui/react";

const AssistantModal = () => (
  <AssistantModalPrimitive.Root>
    <AssistantModalPrimitive.Trigger>
      <FloatingAssistantButton />
    </AssistantModalPrimitive.Trigger>
    <AssistantModalPrimitive.Content>
      <Thread />
    </AssistantModalPrimitive.Content>
  </AssistantModalPrimitive.Root>
);
```

## API Reference

### Root

- `open?`: `boolean | undefined`
- `defaultOpen?`: `boolean | undefined`
- `onOpenChange?`: `{ bivarianceHack(open: boolean): void; }['bivarianceHack'] | undefined`
- `modal?`: `boolean | undefined`
- `unstable_openOnRunStart?`: `boolean | undefined` (experimental)

### Trigger

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

| Data attribute | Values               |
| -------------- | -------------------- |
| `[data-state]` | `"open" \| "closed"` |

### Content

This primitive renders a `<div>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.

- `onOpenAutoFocus?`: `FocusScopeProps['onMountAutoFocus'] | undefined` — Event handler called when auto-focusing on open. Can be prevented.

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

- `onEscapeKeyDown?`: `{ bivarianceHack(event: KeyboardEvent): void; }['bivarianceHack'] | undefined` — Event handler called when the escape key is down. Can be prevented.

- `onPointerDownOutside?`: `{ bivarianceHack(event: PointerDownOutsideEvent): void; }['bivarianceHack'] | undefined` — Event handler called when the a \`pointerdown\` event happens outside of the \`DismissableLayer\`. Can be prevented.

- `onFocusOutside?`: `{ bivarianceHack(event: FocusOutsideEvent): void; }['bivarianceHack'] | undefined` — Event handler called when the focus moves outside of the \`DismissableLayer\`. Can be prevented.

- `onInteractOutside?`: `{ bivarianceHack(event: PointerDownOutsideEvent | FocusOutsideEvent): void; }['bivarianceHack'] | undefined` — Event handler called when an interaction happens outside the \`DismissableLayer\`. Specifically, when a \`pointerdown\` event happens outside or focus moves outside of it. Can be prevented.

- `forceMount?`: `true | undefined` — Used to force mounting when more control is needed. Useful when controlling animation with React animation libraries.

- `deferPointerDownOutside?`: `boolean | undefined` — When \`true\`, a \`'pointerdown'\` event outside of the layered element will wait for the interaction's click event before dispatching, allowing third-party code to stop propagation of later events and cancel dismissal.

- `render?`: `ReactElement | undefined`

- `portalProps?`: `ComponentPropsWithoutRef<typeof PopoverPrimitive.Portal> | undefined`

  - `container?`: `PortalProps['container'] | undefined` — Specify a container element to portal the content into.
  - `forceMount?`: `true | undefined` — Used to force mounting when more control is needed. Useful when controlling animation with React animation libraries.

- `dissmissOnInteractOutside?`: `boolean | undefined`

### Anchor

This primitive renders a `<div>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `virtualRef?`: `React.RefObject<Measurable | null> | undefined`
- `render?`: `ReactElement | undefined`