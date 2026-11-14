# Recommendation card  (`recommendation-card`)

The agent proposes a change with its confidence, and waits for a yes.

- 完整文件：`references/pages/recommendation-card.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-recommendation-card.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-recommendation-card.md`

## With a runtime

A proposal like this is a human-in-the-loop tool call: the model calls it to ask a question, and the card itself supplies the result once the user picks an answer.

```tsx
"use client";

import { defineToolkit } from "@assistant-ui/react";
import { z } from "zod";
import { RecommendationCard } from "@/components/assistant-ui/elements/recommendation-card";

export const toolkit = defineToolkit({
  propose_change: {
    type: "human",
    description: "Propose a change and wait for the user to accept it.",
    parameters: z.object({
      question: z.string(),
      detail: z.string(),
      confidenceLabel: z.string(),
      acceptedLabel: z.string(),
    }),
    render: ({ args, status, result, addResult }) => {
      if (status.type === "running" || status.type === "incomplete") return null;

      return (
        <RecommendationCard
          state={result ? "accepted" : "idle"}
          question={args.question}
          confidenceLabel={args.confidenceLabel}
          acceptedLabel={args.acceptedLabel}
          onAccept={() => addResult({ accepted: true })}
          onAlternatives={() => addResult({ accepted: false })}
        >
          {args.detail}
        </RecommendationCard>
      );
    },
  },
});

```

## Standalone (no runtime)

Standalone, the element is a controlled display: you own `state` and decide what Accept and Alternatives do.

```tsx
"use client";

import { useState } from "react";
import {
  RecommendationCard,
  type RecommendationState,
} from "@/components/assistant-ui/elements/recommendation-card";

export function Recommendation() {
  const [state, setState] = useState<RecommendationState>("idle");

  return (
    <RecommendationCard
      state={state}
      question="Enable draft autosave?"
      confidenceLabel="high confidence"
      acceptedLabel="Enabled for every thread"
      onAccept={() => setState("accepted")}
      onAlternatives={() => showAlternatives()}
    >
      Threads lose their draft on switch. Wiring runtime.drafts fixes it with
      three lines and no migration.
    </RecommendationCard>
  );
}

```
