# Option list  (`option-list`)

The agent asks a question with a few answers; the pick returns to it and stays as a receipt.

- 完整文件：`references/pages/option-list.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-option-list.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-option-list.md`

## With a runtime

The question is a human tool: the model supplies the options as arguments, the run pauses, and `addResult` sends the pick back as the tool's result. The same result drives the receipt.

```tsx
"use client";

import { defineToolkit, useAuiState } from "@assistant-ui/react";
import { z } from "zod";
import { OptionList } from "@/components/assistant-ui/elements/option-list";

export const toolkit = defineToolkit({
  ask_user_to_choose: {
    type: "human",
    description:
      "Ask the user a question with a few answers. Put the question itself in your message, not in the options.",
    parameters: z.object({
      options: z.array(
        z.object({
          id: z.string(),
          label: z.string(),
          description: z.string().optional(),
        }),
      ),
      selectionMode: z.enum(["single", "multiple"]).optional(),
    }),
    render: function AskUserToChoose({ args, status, result, addResult }) {
      const canAnswer = useAuiState(
        (s) => s.thread.capabilities.answerToolCall,
      );
      const waiting = status.type === "requires-action" && canAnswer;

      return (
        <OptionList
          options={args.options ?? []}
          selectionMode={args.selectionMode}
          choice={result?.selected}
          onConfirm={
            waiting ? (ids) => addResult({ selected: ids }) : undefined
          }
        />
      );
    },
  },
});

```

## Standalone (no runtime)

Standalone, `onConfirm` receives the pick and a fulfilled confirmation settles into a receipt. `choice` lets your application supply the receipt and takes precedence when it is set.

```tsx
"use client";

import { useState } from "react";
import { OptionList } from "@/components/assistant-ui/elements/option-list";

const OPTIONS = [
  { id: "merge", label: "Merge duplicates" },
  { id: "keep", label: "Keep both" },
  { id: "review", label: "Review each pair" },
];

export function Duplicates() {
  const [choice, setChoice] = useState<string[]>();

  return (
    <OptionList
      aria-label="How should I handle the duplicate contacts?"
      options={OPTIONS}
      choice={choice}
      onConfirm={setChoice}
    />
  );
}

```
