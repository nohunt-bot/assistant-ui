# Question flow  (`question-flow`)

Ask a few short questions in sequence and return every answer as one result.

- 完整文件：`references/pages/question-flow.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-question-flow.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-question-flow.md`

## With a runtime

A question flow is a human tool: the model supplies its steps as arguments, the run pauses when the arguments are complete, and the final step returns `{ answers }` as the tool result.

```tsx
"use client";

import { defineToolkit, useAuiState } from "@assistant-ui/react";
import { z } from "zod";
import { QuestionFlow } from "@/components/assistant-ui/elements/question-flow";

export const toolkit = defineToolkit({
  ask_project_questions: {
    type: "human",
    description: "Ask a short sequence of questions before planning the project update.",
    parameters: z.object({
      steps: z.array(
        z.object({
          id: z.string(),
          question: z.string(),
          description: z.string().optional(),
          options: z.array(
            z.object({
              id: z.string(),
              label: z.string(),
              description: z.string().optional(),
            }),
          ),
          selectionMode: z.enum(["single", "multiple"]).optional(),
          minSelections: z.number().optional(),
          maxSelections: z.number().optional(),
        }),
      ),
    }),
    render: function AskProjectQuestions({ args, status, result, addResult }) {
      const canAnswer = useAuiState(
        (s) => s.thread.capabilities.answerToolCall,
      );
      const waiting = status.type === "requires-action" && canAnswer;

      return (
        <QuestionFlow
          steps={args.steps ?? []}
          choice={result?.answers}
          onComplete={
            waiting ? (answers) => addResult({ answers }) : undefined
          }
        />
      );
    },
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, a fulfilled `onComplete` settles the flow into its receipt. Hold completed answers in your own state and pass them to `choice` when your application needs to supply or override that receipt.

```tsx
"use client";

import { useState } from "react";
import {
  QuestionFlow,
  type QuestionFlowStep,
} from "@/components/assistant-ui/elements/question-flow";

const STEPS: QuestionFlowStep[] = [
  {
    id: "audience",
    question: "Who should receive this update?",
    options: [
      { id: "team", label: "The project team" },
      { id: "leaders", label: "Department leaders" },
    ],
  },
  {
    id: "topics",
    question: "What should it cover?",
    selectionMode: "multiple",
    options: [
      { id: "milestones", label: "Milestones" },
      { id: "risks", label: "Open risks" },
    ],
  },
];

export function ProjectUpdateQuestions() {
  const [choice, setChoice] = useState<Record<string, string[]>>();

  return (
    <QuestionFlow
      steps={STEPS}
      choice={choice}
      onComplete={setChoice}
    />
  );
}

```
