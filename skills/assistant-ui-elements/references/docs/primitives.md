# Overview
URL: /docs/primitives

Unstyled, accessible building blocks for AI chat interfaces, such as Thread, Composer, and Message.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

Primitives are the unstyled layer of assistant-ui: accessible React components that handle all the wiring of AI chat, including state management, keyboard shortcuts, auto-scrolling, streaming, and tool calls, and leave every visual decision to you.

## Primitives or elements?

assistant-ui ships two layers:

- **[Elements](/elements)** are styled, installable components built from primitives. Each element's page shows a live demo, its install command, and both ways to use it: wired to a runtime, or driven by plain props. If you want ready-made UI, or anything else element-related, the [element catalog](/elements) is the place to look; this section never duplicates it.
- **Primitives** (this section) are what elements are made of. Reach for them when the UI you need doesn't exist as an element, such as a floating composer, a custom message layout, or an inline editing experience.

```
import { ComposerPrimitive } from "@assistant-ui/react";

<ComposerPrimitive.Root>
  <ComposerPrimitive.Input placeholder="Ask anything..." />
  <ComposerPrimitive.Send>Send</ComposerPrimitive.Send>
</ComposerPrimitive.Root>
```

This renders an unstyled `<form>` with a `<textarea>` and a `<button>`. It has no styles, and it already handles submit on Enter, focus management, empty-state disabling, and streaming state.

## How they work

Every primitive follows the same pattern inspired by [Radix UI](https://www.radix-ui.com/):

- **`Primitive.Root`**: container that provides context to child parts
- **`Primitive.PartName`**: individual elements (input, button, text, etc.)
- **`asChild`**: merge primitive behavior onto your own element instead of rendering a wrapper
- **`AuiIf`**: conditional rendering based on state

Primitives read from the nearest runtime context. Place them inside an `AssistantRuntimeProvider` and they work without prop drilling or manual state wiring.

## Available primitives

- [Composer](/docs/primitives/composer) —

  Text input, send button, attachments, and dictation. The interface for composing new messages or editing existing ones.

- [Thread](/docs/primitives/thread) —

  The scrollable message container with auto-scroll, empty states, and message rendering.

- [Message](/docs/primitives/message) —

  Individual message rendering with role-based content, parts, and metadata.

- [ActionBar](/docs/primitives/action-bar) —

  Copy, reload, edit, and other message actions.

- [BranchPicker](/docs/primitives/branch-picker) —

  Navigate between message branches (alternative responses).

- [ThreadList](/docs/primitives/thread-list) —

  Multi-thread management: list, create, switch, and archive threads.

- [AssistantModal](/docs/primitives/assistant-modal) —

  Floating chat popover built on Radix UI Popover.

- [Attachment](/docs/primitives/attachment) —

  File and image attachment rendering.

- [Suggestion](/docs/primitives/suggestion) —

  Suggested prompts and quick actions.

- [SelectionToolbar](/docs/primitives/selection-toolbar) —

  Floating toolbar for text selection actions like quoting.

- [Error](/docs/primitives/error) —

  Error display with accessible alert role and automatic error text.

- [ChainOfThought](/docs/primitives/chain-of-thought) —

  Collapsible reasoning accordion for thinking steps and tool calls.

`MessagePartPrimitive` (for rendering individual text, image, and streaming parts) is documented within the [Message](/docs/primitives/message#messagepartprimitive) primitive page.

## Related primitive references

- [MessagePartPrimitive](/docs/api-reference/primitives/message-part)
- [ThreadListItemPrimitive](/docs/api-reference/primitives/thread-list-item)
- [ThreadListItemMorePrimitive](/docs/api-reference/primitives/thread-list-item-more)
- [ActionBarMorePrimitive](/docs/api-reference/primitives/action-bar-more)
- [AuiIf](/docs/api-reference/primitives/assistant-if)

## Common mistakes

- Forgetting to wrap primitives with runtime context (`AssistantRuntimeProvider` + runtime hook)
- Mixing deprecated props with current APIs (`submitOnEnter`, `autoSend`, `components` on iterators such as `ThreadPrimitive.Messages`)
- Mounting primitives in the wrong context (`ActionBarPrimitive` / `BranchPickerPrimitive` outside `MessagePrimitive.Root`)
- Using unstable props unintentionally (`unstable_*` APIs)

## Next steps

Browse the [element catalog](/elements) if a ready-made component covers your case. Otherwise start with the [Composer](/docs/primitives/composer) primitive to see how primitives work in practice, or jump to the [API Reference](/docs/api-reference/primitives/composer) for full prop details.