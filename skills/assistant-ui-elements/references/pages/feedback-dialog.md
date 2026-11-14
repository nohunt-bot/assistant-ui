# Feedback dialog
URL: /elements/feedback-dialog

A thumbs-down that asks why, so the signal arrives with a reason attached.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

FeedbackDialog turns a thumbs-down into a short form: pick what went wrong, add an optional note, and send. With a runtime the binary rating reaches the thread through `submitFeedback`; standalone you own the selection, the note, and what submitting does.

## Getting started

**With a runtime:**

assistant-ui's own feedback signal is binary: positive or negative, recorded on the message. `ActionBarPrimitive.FeedbackNegative` already submits that in one click. FeedbackDialog asks why first, so it sits in front of that signal rather than replacing it: keep the binary rating for the runtime, and pass the reasons and note as `comment`.

1. ### Open the panel from a trigger

   A separate trigger owns the open state. `FeedbackDialog` itself has no `open` prop; it always renders its form (or its confirmation) once mounted.

   ```
   "use client";

   import { useState } from "react";
   import { useAui } from "@assistant-ui/react";
   import { FeedbackDialog } from "@/components/assistant-ui/elements/feedback-dialog";

   const REASONS = [
     "Not factual",
     "Didn't follow instructions",
     "Too long",
     "Unsafe",
   ] as const;

   export function MessageFeedback() {
     const aui = useAui();
     const [open, setOpen] = useState(false);
     const [selected, setSelected] = useState<string[]>([]);
     const [note, setNote] = useState("");
     const [sent, setSent] = useState(false);

     if (!open) {
       return (
         <button type="button" onClick={() => setOpen(true)}>
           Report an issue
         </button>
       );
     }

     return (
       <FeedbackDialog
         reasons={REASONS}
         selected={selected}
         note={note}
         sent={sent}
         onToggleReason={(reason) =>
           setSelected((current) =>
             current.includes(reason)
               ? current.filter((item) => item !== reason)
               : [...current, reason],
           )
         }
         onNoteChange={setNote}
         onSubmit={() => {
           const comment = [...selected, note.trim()]
             .filter(Boolean)
             .join(". ")
             .slice(0, 2000);
           aui.message.submitFeedback({ type: "negative", comment });
           setSent(true);
         }}
       />
     );
   }
   ```

2. ### Register where feedback lands

   `submitFeedback` reaches a `FeedbackAdapter` you register on the runtime. Its `submit` receives the message, type, and optional `comment`, so reasons and a note travel in `comment`. Structured reasons still need their own transport when they must remain separate. The example caps the comment at 2,000 characters, the longest Assistant Cloud accepts; a backend with another limit sets its own.

   ```
   import { useLocalRuntime } from "@assistant-ui/react";

   const runtime = useLocalRuntime(adapter, {
     adapters: {
       feedback: {
         submit: ({ message, type, comment }) => {
           fetch("/api/feedback", {
             method: "POST",
             body: JSON.stringify({ messageId: message.id, type, comment }),
           });
         },
       },
     },
   });
   ```

**Standalone (no runtime):**

Standalone, FeedbackDialog is fully controlled: you hold `selected`, `note`, and `sent`, and `onSubmit` is called with no arguments, so it reads your own state rather than receiving a payload.

1. ### Hold the form state

   ```
   "use client";

   import { useState } from "react";
   import { FeedbackDialog } from "@/components/assistant-ui/elements/feedback-dialog";

   const REASONS = ["Not factual", "Didn't follow instructions", "Too long", "Unsafe"];

   export function AnswerFeedback() {
     const [selected, setSelected] = useState<string[]>([]);
     const [note, setNote] = useState("");
     const [sent, setSent] = useState(false);

     return (
       <FeedbackDialog
         reasons={REASONS}
         selected={selected}
         note={note}
         sent={sent}
         onToggleReason={(reason) =>
           setSelected((current) =>
             current.includes(reason)
               ? current.filter((item) => item !== reason)
               : [...current, reason],
           )
         }
         onNoteChange={setNote}
         onSubmit={() => sendFeedback({ selected, note })}
       />
     );
   }
   ```

2. ### Send it and flip `sent`

   ```
   async function sendFeedback(payload: { selected: string[]; note: string }) {
     await fetch("/api/feedback", { method: "POST", body: JSON.stringify(payload) });
     setSent(true);
   }
   ```

## Anatomy

```
<div data-slot="feedback-dialog">
  <div role="status">{/* mounted in both states, so a screen reader catches "sent" the instant it flips */}</div>
  {/* not sent: a header, wrapped reason toggle pills, a note textarea, a submit button */}
  {/* sent: the form is gone; only the confirmation line remains */}
</div>
```

Reasons toggle independently as a multi-select (`aria-pressed`, any number active at once); there is no minimum. Once `sent` is true the form disappears entirely, so reopening it for a second report means remounting with `sent` back to `false`. The live region is present in the tree before and after the change, not created alongside it, which is what lets assistive tech announce the confirmation reliably.

## Examples

### Auto-reset after sending

Clearing `sent`, `selected`, and `note` a few seconds after a send lets the same trigger open a fresh form next time:

```
useEffect(() => {
  if (!sent) return;
  const id = setTimeout(() => {
    setSent(false);
    setSelected([]);
    setNote("");
  }, 2600);
  return () => clearTimeout(id);
}, [sent]);
```

### Receiving feedback on the server

**With a runtime:**

An external-store runtime takes the same `feedback` adapter shape as `useLocalRuntime`:

```
import { useExternalStoreRuntime } from "@assistant-ui/react";

const runtime = useExternalStoreRuntime({
  // ...messages, onNew, isRunning, etc.
  adapters: {
    feedback: {
      submit: ({ message, type, comment }) => api.rateMessage(message.id, type, comment),
    },
  },
});
```

Without a registered adapter, `submitFeedback` still updates `message.metadata.submittedFeedback` locally, so `ActionBarPrimitive.FeedbackNegative`'s pressed state stays correct even if you never wire a server side.

### Restyle the panel

Both lanes take `className` on the root. Pills, the textarea, and the submit button read the shared `field`, `inkButton`, and `mono` surfaces from `surfaces.tsx`, so retinting those tokens retints every element that uses them.

```
<FeedbackDialog className="max-w-md" /* ... */ />
```

## API reference

**With a runtime:**

### Message state

| Selector                               | Type                                                                       | Description                                                                   |
| -------------------------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `s.message.metadata.submittedFeedback` | `{ type: "positive" \| "negative"; comment?: string } \| undefined`        | The rating already recorded for this message, if any.                         |
| `aui.message.submitFeedback(feedback)` | `(feedback: { type: "positive" \| "negative"; comment?: string }) => void` | Records the rating on the message and calls the registered `FeedbackAdapter`. |

### FeedbackAdapter

| Field    | Type                                                                                               | Description                                                                                                                                                                 |
| -------- | -------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `submit` | `(feedback: { message: ThreadMessage; type: "positive" \| "negative"; comment?: string }) => void` | Called once per `submitFeedback`. Register it under `adapters.feedback` on `useLocalRuntime` or an external-store runtime. Carries the message, type, and optional comment. |

**Standalone (no runtime):**

### FeedbackDialog

| Prop             | Type                       | Default  | Description                                                                                                                                                |
| ---------------- | -------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `reasons`        | `readonly string[]`        | required | The reason pills offered.                                                                                                                                  |
| `selected`       | `readonly string[]`        | required | Reasons currently pressed.                                                                                                                                 |
| `note`           | `string`                   | required | The textarea's value.                                                                                                                                      |
| `sent`           | `boolean`                  | required | Swaps the form for the confirmation line when true.                                                                                                        |
| `onToggleReason` | `(reason: string) => void` |          | Called with the pressed reason; toggling membership in `selected` is the caller's job. Without it, reasons render as non-interactive selected-state chips. |
| `onNoteChange`   | `(note: string) => void`   |          | Called with the textarea's next value.                                                                                                                     |
| `onSubmit`       | `() => void`               |          | Called with no arguments. Read `selected` and `note` from your own state. The submit button renders only when this is supplied.                            |
| `className`      | `string`                   |          | Merged onto the root.                                                                                                                                      |

All other `div` props are forwarded to the root.