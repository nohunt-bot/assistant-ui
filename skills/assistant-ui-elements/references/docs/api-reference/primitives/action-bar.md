# ActionBarPrimitive
URL: /docs/api-reference/primitives/action-bar

Composable message action controls for copy, edit, reload, speech, and feedback in assistant-ui chat interfaces.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

For examples and usage patterns, see [ActionBar](/docs/primitives/action-bar).

## Anatomy

```
import { ActionBarPrimitive } from "@assistant-ui/react";

const UserMessageBar = () => (
  <ActionBarPrimitive.Root>
    <ActionBarPrimitive.Edit />
    <ActionBarPrimitive.Copy />
  </ActionBarPrimitive.Root>
);

const AssistantMessageBar = () => (
  <ActionBarPrimitive.Root>
    <ActionBarPrimitive.Reload />
    <ActionBarPrimitive.Copy />
  </ActionBarPrimitive.Root>
);
```

## API Reference

### Root

The root container for action bar components. This component provides intelligent visibility and floating behavior for action bars, automatically hiding and showing based on message state, hover status, and configuration. It supports floating mode for better UX when space is limited.

This primitive renders a `<div>` element unless `asChild` is set.

```
<ActionBarPrimitive.Root
  hideWhenRunning={true}
  autohide="not-last"
  autohideFloat="single-branch"
>
  <ActionBarPrimitive.Copy />
  <ActionBarPrimitive.Edit />
  <ActionBarPrimitive.Reload />
</ActionBarPrimitive.Root>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`
- `hideWhenRunning`: `boolean | undefined` (default `false`) — Whether to hide the action bar when the thread is running, or while another tab or session is still writing this message.
- `autohide`: `"always" | "not-last" | "never" | undefined` (default `"never"`) — Controls when the action bar should automatically hide. - "always": Always hide unless hovered - "not-last": Hide unless this is the last message - "never": Never auto-hide
- `autohideFloat`: `"always" | "single-branch" | "never" | undefined` (default `"never"`) — Controls floating behavior when auto-hidden. - "always": Always float when hidden - "single-branch": Float only for single-branch messages - "never": Never float

| Data attribute    | Values                |
| ----------------- | --------------------- |
| `[data-floating]` | Present when floating |

### Copy

A button component that copies message content to the clipboard. This component automatically handles copying message text to the clipboard and provides visual feedback through the data-copied attribute. It's disabled when there's no copyable content available.

This primitive renders a `<button>` element unless `asChild` is set.

```
<ActionBarPrimitive.Copy copiedDuration={2000}>
  Copy Message
</ActionBarPrimitive.Copy>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`
- `copiedDuration?`: `number | undefined`

| Data attribute  | Values                                        |
| --------------- | --------------------------------------------- |
| `[data-copied]` | Present when the message was recently copied. |

### Reload

A button component that reloads/regenerates the current assistant message. This component automatically handles reloading the current assistant message and is disabled when reloading is not available (e.g., thread is running, disabled, or message is not from assistant).

This primitive renders a `<button>` element unless `asChild` is set.

```
<ActionBarPrimitive.Reload>
  Reload Message
</ActionBarPrimitive.Reload>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Resume

A button that answers again in place of an assistant message whose run stopped before it wrote anything (its tab closed or lost its connection). It deletes the empty reply and its stored row, then runs again from the reply's parent, so no branch is left beside it; a runtime that cannot delete messages starts a new branch instead. It renders disabled unless the message is the last one on its branch and its run went \`staleAfter\` without a heartbeat. A reply that wrote content before its run stopped is a regular error, answered again with Regenerate.

This primitive renders a `<button>` element unless `asChild` is set.

```
<MessagePrimitive.Error>
  <ErrorPrimitive.Root>
    <ErrorPrimitive.Message />
    <ActionBarPrimitive.Resume>Resume</ActionBarPrimitive.Resume>
  </ErrorPrimitive.Root>
</MessagePrimitive.Error>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### RefreshRun

A button that re-reads an assistant message whose run another tab or session is still writing, so the thread shows its latest stored state without a page reload. The thread also re-reads it on its own around the moment the run would cross \`staleAfter\`. It renders disabled unless Assistant Cloud reported the run in progress elsewhere, and while a refresh is in flight.

This primitive renders a `<button>` element unless `asChild` is set.

```
<ActionBarPrimitive.RefreshRun>Refresh</ActionBarPrimitive.RefreshRun>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Edit

A button component that starts editing the current message. This component automatically handles starting the edit mode for the current message and is disabled when editing is not available (e.g., already in editing mode).

This primitive renders a `<button>` element unless `asChild` is set.

```
<ActionBarPrimitive.Edit>
  Edit Message
</ActionBarPrimitive.Edit>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Speak

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### StopSpeaking

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### FeedbackPositive

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

| Data attribute     | Values                                        |
| ------------------ | --------------------------------------------- |
| `[data-submitted]` | Present when positive feedback was submitted. |

### FeedbackNegative

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

| Data attribute     | Values                                        |
| ------------------ | --------------------------------------------- |
| `[data-submitted]` | Present when negative feedback was submitted. |

### ExportMarkdown

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`
- `filename?`: `string | undefined`
- `onExport?`: `((content: string) => void | Promise<void>) | undefined`