# ThreadPrimitive
URL: /docs/api-reference/primitives/thread

Thread primitives for rendering chat transcripts, message lists, viewport state, suggestions, and composers in assistant-ui.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

For examples and usage patterns, see [Thread](/docs/primitives/thread).

## Anatomy

```
import { ThreadPrimitive } from "@assistant-ui/react";

const Thread = () => (
  <ThreadPrimitive.Root>
    <ThreadPrimitive.Viewport>
      <AuiIf condition={(s) => s.thread.isEmpty}>...</AuiIf>
      <ThreadPrimitive.Messages>{...}</ThreadPrimitive.Messages>
      <ThreadPrimitive.ViewportFooter className="sticky bottom-0">
        <Composer />
      </ThreadPrimitive.ViewportFooter>
    </ThreadPrimitive.Viewport>
  </ThreadPrimitive.Root>
);
```

## API Reference

### Root

The root container component for a thread. This component serves as the foundational wrapper for all thread-related components. It provides the basic structure and context needed for thread functionality. While this component is mounted, an unhandled Escape keydown inside this thread stops its active speech, even if the action bar that started it is no longer mounted. Escape outside every thread retains the document-level fallback.

This primitive renders a `<div>` element unless `asChild` is set.

```
<ThreadPrimitive.Root>
  <ThreadPrimitive.Viewport>
    <ThreadPrimitive.Messages>
      {() => <MyMessage />}
    </ThreadPrimitive.Messages>
  </ThreadPrimitive.Viewport>
</ThreadPrimitive.Root>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Empty

> [!warn]
>
> **Deprecated.** Use `<AuiIf condition={(s) => s.thread.isEmpty} />` instead.



### If

> [!warn]
>
> **Deprecated.** Use `<AuiIf condition={(s) => s.thread...} />` instead.

- `running?`: `boolean | undefined`
- `disabled?`: `boolean | undefined`
- `empty?`: `boolean | undefined`

### Viewport

A scrollable viewport container for thread messages. This component provides a scrollable area for displaying thread messages with automatic scrolling capabilities. It manages the viewport state and provides context for child components to access viewport-related functionality.

This primitive renders a `<div>` element unless `asChild` is set.

```
<ThreadPrimitive.Viewport turnAnchor="top">
  <ThreadPrimitive.Messages>
    {() => <MyMessage />}
  </ThreadPrimitive.Messages>
</ThreadPrimitive.Viewport>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.

- `render?`: `ReactElement | undefined`

- `autoScroll?`: `boolean | undefined` — Whether to automatically scroll to the bottom when new messages are added. When enabled, the viewport will automatically scroll to show the latest content. Default false if \`turnAnchor\` is "top", otherwise defaults to true.

- `turnAnchor?`: `"top" | "bottom" | undefined` — Controls scroll anchoring behavior for new messages. - "bottom" (default): Messages anchor at the bottom, classic chat behavior. - "top": New user messages anchor at the top of the viewport for a focused reading experience.

- `topAnchorMessageClamp`: `ThreadPrimitiveViewportProps["topAnchorMessageClamp"]` (default `{ tallerThan: "10em", visibleHeight: "6em" }`) — Clamps tall user messages so the assistant response stays in view.

  - `tallerThan`: `string` (default `"10em"`) — Clamp messages taller than this. Supports \`px\`, \`em\`, and \`rem\`.
  - `visibleHeight`: `string` (default `"6em"`) — Visible portion of clamped messages. Supports \`px\`, \`em\`, and \`rem\`.

- `scrollToBottomOnRunStart?`: `boolean | undefined` — Whether to scroll to bottom when a new run starts. Defaults to true.

- `scrollToBottomOnInitialize?`: `boolean | undefined` — Whether to scroll to bottom when thread history is first loaded. Defaults to true.

- `scrollToBottomOnThreadSwitch?`: `boolean | undefined` — Whether to scroll to bottom when switching to a different thread. Defaults to true.

### ViewportProvider

- `options?`: `ThreadViewportStoreOptions`

  - `turnAnchor?`: `"top" | "bottom" | undefined`

  - `topAnchorMessageClamp?`: `ThreadViewportStoreOptions["topAnchorMessageClamp"]`

    - `tallerThan?`: `string | undefined`
    - `visibleHeight?`: `string | undefined`

### ViewportFooter

A footer container that measures its height for scroll calculations. This component measures its height and provides it to the viewport context so the auto-scroll system can account for any sticky footer overlapping the message list. Multiple ViewportFooter components can be used - their heights are summed. Typically used with \`className="sticky bottom-0"\` to keep the footer visible at the bottom of the viewport while scrolling.

This primitive renders a `<div>` element unless `asChild` is set.

```
<ThreadPrimitive.Viewport>
  <ThreadPrimitive.Messages>
    {() => <MyMessage />}
  </ThreadPrimitive.Messages>
  <ThreadPrimitive.ViewportFooter className="sticky bottom-0">
    <Composer />
  </ThreadPrimitive.ViewportFooter>
</ThreadPrimitive.Viewport>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Messages

- `components?`: `MessagesComponentConfig` (deprecated: Use the children render function instead.)

  - `Message?`: `ComponentType` — Component used to render all message types
  - `EditComposer?`: `ComponentType | undefined` — Component used when editing any message type
  - `UserEditComposer?`: `ComponentType | undefined` — Component used when editing user messages specifically
  - `AssistantEditComposer?`: `ComponentType | undefined` — Component used when editing assistant messages specifically
  - `SystemEditComposer?`: `ComponentType | undefined` — Component used when editing system messages specifically
  - `UserMessage?`: `ComponentType | undefined` — Component used to render user messages specifically
  - `AssistantMessage?`: `ComponentType | undefined` — Component used to render assistant messages specifically
  - `SystemMessage?`: `ComponentType | undefined` — Component used to render system messages specifically

- `children?`: `(value: { message: MessageState }) => ReactNode` — Render function called for each message. Receives the message.

### MessageByIndex

Renders a single message at the specified index in the current thread.

- `index`: `number`

- `components`: `MessagesComponentConfig`

  - `Message?`: `ComponentType` — Component used to render all message types
  - `EditComposer?`: `ComponentType | undefined` — Component used when editing any message type
  - `UserEditComposer?`: `ComponentType | undefined` — Component used when editing user messages specifically
  - `AssistantEditComposer?`: `ComponentType | undefined` — Component used when editing assistant messages specifically
  - `SystemEditComposer?`: `ComponentType | undefined` — Component used when editing system messages specifically
  - `UserMessage?`: `ComponentType | undefined` — Component used to render user messages specifically
  - `AssistantMessage?`: `ComponentType | undefined` — Component used to render assistant messages specifically
  - `SystemMessage?`: `ComponentType | undefined` — Component used to render system messages specifically

### Unstable_MessageById

> [!tip]
>
> **Experimental.** Shipped 2026-06-23; it may change in any release.

Renders the message with the given id in the current thread. Unlike ThreadPrimitiveMessageByIndex, this keys off the message id, so it stays attached to the same message across reordering and windowing - the shape needed to drive a virtualized or custom message list together with \`unstable_useThreadMessageIds\`. A missing or removed id renders \`null\` rather than throwing.

```
const messageIds = unstable_useThreadMessageIds();
return messageIds.map((messageId) => (
  <ThreadPrimitive.Unstable_MessageById
    key={messageId}
    messageId={messageId}
    components={MESSAGE_COMPONENTS}
  />
));
```

- `messageId`: `string`

- `components`: `MessagesComponentConfig`

  - `Message?`: `ComponentType` — Component used to render all message types
  - `EditComposer?`: `ComponentType | undefined` — Component used when editing any message type
  - `UserEditComposer?`: `ComponentType | undefined` — Component used when editing user messages specifically
  - `AssistantEditComposer?`: `ComponentType | undefined` — Component used when editing assistant messages specifically
  - `SystemEditComposer?`: `ComponentType | undefined` — Component used when editing system messages specifically
  - `UserMessage?`: `ComponentType | undefined` — Component used to render user messages specifically
  - `AssistantMessage?`: `ComponentType | undefined` — Component used to render assistant messages specifically
  - `SystemMessage?`: `ComponentType | undefined` — Component used to render system messages specifically

### Row

- `row`: `ThreadRow` — A row from \`createThreadRowsSelector\`.

  - `type`: `"message"`
  - `key`: `string` — Stable across streaming and earlier pages; use it as the list key.
  - `messageId`: `string` — The turn's last message, whose scope the row renders in.
  - `turnMessageId`: `string` — The first message of the row's turn.

- `children`: `(info: RenderInfo) => ReactNode` — Render function for the row; keep its identity stable across renders.

### ScrollToBottom

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`
- `behavior?`: `ScrollBehavior | undefined`

### LoadEarlier

A button that loads the page before the first loaded message. Disabled while the runtime reports no earlier messages. A press while a page is loading joins that load, so the button stays enabled and keeps keyboard focus until the page lands; read \`thread.isLoadingEarlier\` to show progress.

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Suggestion

This primitive renders a `<button>` element unless `asChild` is set.

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`
- `prompt`: `string` — The suggestion prompt.
- `send?`: `boolean | undefined` — When true, automatically sends the message. When false, replaces or appends the composer text with the suggestion - depending on the value of \`clearComposer\`.
- `clearComposer`: `boolean | undefined` (default `true`) — Whether to clear the composer after sending. A send queued while a run is in progress never clears the composer. When send is set to false, determines if composer text is replaced with suggestion (true, default), or if it's appended to the composer text (false).
- `autoSend?`: `boolean | undefined` (deprecated: Use \`send\` instead.)
- `method?`: `"replace"` (deprecated: Use \`clearComposer\` instead.)

### Suggestions

- `components?`: `SuggestionsComponentConfig` (deprecated: Use the children render function instead.)
  - `Suggestion`: `ComponentType` — Component used to render each suggestion
- `children?`: `(value: { suggestion: SuggestionState }) => ReactNode` — Render function called for each suggestion. Receives the suggestion.

### SuggestionByIndex

Renders a single suggestion at the specified index.

- `index`: `number`
- `components`: `SuggestionsComponentConfig`
  - `Suggestion`: `ComponentType` — Component used to render each suggestion