# Guardrail notice
URL: /elements/guardrail-notice

A refusal in its own shape, with the nearest thing it can do instead.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

An icon, a title, a short policy tag, an explanation, and an optional list of alternatives the reader can pick instead. Unlike a generic failure banner (see `/elements/error-state`), this is specifically for a policy stop rather than a transport or server error. With a runtime you detect the stop from the message's own status; standalone you decide when to show it.

## Getting started

**With a runtime:**

A refusal is not a separate concept from a normal message: it is an assistant message whose status finished as `"incomplete"` with reason `"content-filter"`. Everything else on the notice, the title, the explanation, and the alternatives, is content your app supplies; the runtime only tells you that the stop happened.

1. ### Detect a content filter stop

   ```
   "use client";

   import { useAuiState } from "@assistant-ui/react";

   function useIsGuardrailStop() {
     return useAuiState((s) => {
       const status = s.message.status;
       return status?.type === "incomplete" && status.reason === "content-filter";
     });
   }
   ```

2. ### Send an alternative back

   ```
   import { useAui } from "@assistant-ui/react";
   import { GuardrailNotice } from "@/components/assistant-ui/elements/guardrail-notice";

   const ALTERNATIVES = ["Explain the defensive side of the same problem"];

   function AssistantGuardrail() {
     const isGuardrailStop = useIsGuardrailStop();
     const aui = useAui();
     if (!isGuardrailStop) return null;

     return (
       <GuardrailNotice
         title="I can't help with that"
         explanation="This asks for something I can't assist with directly."
         policy="content-filter"
         alternatives={ALTERNATIVES}
         onPick={(alternative) => aui.thread.append(alternative)}
       />
     );
   }
   ```

   `aui.thread.append(text)` sends `text` as a new user message and starts a run with it, the same as if the reader had typed and sent it themselves.

**Standalone (no runtime):**

Standalone, showing the notice at all, and what it says, is entirely your call.

1. ### Hold the refusal state

   ```
   "use client";

   import { GuardrailNotice } from "@/components/assistant-ui/elements/guardrail-notice";

   export function RefusalNotice() {
     return (
       <GuardrailNotice
         title="I can't help with that"
         explanation="This asks for a working exploit against infrastructure you don't own."
         policy="policy"
         alternatives={["Explain how to defend against this instead"]}
       />
     );
   }
   ```

2. ### Wire onPick to your own retry

   ```
   <GuardrailNotice
     /* ... */
     onPick={(alternative) => sendMessage(alternative)}
   />
   ```

   `onPick` is optional; omitting it renders the alternatives as non-interactive suggestions instead of buttons.

## Anatomy

```
<div data-slot="guardrail-notice">
  <div>{/* shield icon, title, policy tag */}</div>
  <p>{/* explanation, always rendered */}</p>
  <div>{/* "try instead" label + one button per alternative, only when alternatives.length > 0 */}</div>
</div>
```

The alternatives block is omitted when `alternatives` is an empty array; the notice then ends after the explanation paragraph. Each alternative renders as a button when `onPick` is provided and as non-interactive text otherwise.

## Examples

### Reading the refusal reason

`status.reason` on an incomplete message is one of a fixed set of strings; `"content-filter"` is the one that maps to a guardrail stop specifically, distinct from `"cancelled"`, `"tool-calls"`, `"length"`, `"error"`, or `"other"`.

```
const policy = status.type === "incomplete" ? status.reason : undefined;
```

### Restyle the notice

Both lanes take `className` on the root, which starts as `flex w-full max-w-sm flex-col gap-3 rounded-[20px]` on the shared `paper` surface. The policy tag reads the `mono` token from `surfaces.tsx`; the shield icon's amber tint is set in the component.

```
<GuardrailNotice className="max-w-none rounded-2xl" /* ... */ />
```

## API reference

**With a runtime:**

### Message state

| Selector / call              | Type                                     | Description                                                                                            |
| ---------------------------- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `s.message.status`           | `MessageStatus \| undefined`             | `type === "incomplete"` with `reason === "content-filter"` marks a guardrail stop.                     |
| `aui.thread.append(message)` | `(message: CreateAppendMessage) => void` | Sends a plain string (or a full message) as a new user turn; use it to send a picked alternative back. |

**Standalone (no runtime):**

### GuardrailNotice

| Prop           | Type                            | Default  | Description                                                                                                  |
| -------------- | ------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------ |
| `title`        | `string`                        | required | Headline next to the shield icon.                                                                            |
| `explanation`  | `string`                        | required | Body text; always rendered.                                                                                  |
| `policy`       | `string`                        | required | Short tag shown at the end of the header row.                                                                |
| `alternatives` | `readonly string[]`             | required | Suggested next prompts. An empty array omits the whole block.                                                |
| `onPick`       | `(alternative: string) => void` |          | Called with the selected alternative's text. Without it, alternatives render as non-interactive suggestions. |
| `className`    | `string`                        |          | Merged onto the root.                                                                                        |

All other `div` props except `children`, `title`, `explanation`, `policy`, `alternatives`, and `onPick` are forwarded to the root.