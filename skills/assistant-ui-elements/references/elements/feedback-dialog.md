# Feedback dialog  (`feedback-dialog`)

A thumbs-down that asks why, so the signal arrives with a reason attached.

- 完整文件：`references/pages/feedback-dialog.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-feedback-dialog.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-feedback-dialog.md`

## With a runtime

assistant-ui's own feedback signal is binary: positive or negative, recorded on the message. `ActionBarPrimitive.FeedbackNegative` already submits that in one click. FeedbackDialog asks why first, so it sits in front of that signal rather than replacing it: keep the binary rating for the runtime, and pass the reasons and note as `comment`.

```tsx
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
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, FeedbackDialog is fully controlled: you hold `selected`, `note`, and `sent`, and `onSubmit` is called with no arguments, so it reads your own state rather than receiving a payload.

```tsx
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
