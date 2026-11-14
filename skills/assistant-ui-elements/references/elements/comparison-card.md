# Comparison  (`comparison-card`)

Two options weighed side by side, with the pick named and argued.

- 完整文件：`references/pages/comparison-card.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-comparison-card.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-comparison-card.md`

## With a runtime

This element has no assistant-ui primitive of its own, so the runtime wiring is a tool renderer rather than a primitive composition. A comparison has no natural row by row reveal, and option cards filling in out of order as JSON streams in would read as broken, so wait for the call to finish before mounting the card.

```tsx
"use client";

import { defineToolkit } from "@assistant-ui/react";
import { ComparisonCard } from "@/components/assistant-ui/elements/comparison-card";

export const toolkit = defineToolkit({
  compare_options: {
    type: "backend",
    render: ({ args, status }) => {
      if (status.type !== "complete") return null;
      return <ComparisonCard {...args} />;
    },
  },
});

```

## Standalone (no runtime)

Standalone, the element is a plain display component: it owns no state of its own. Unlike the other structured output elements it has no reveal prop; every option and every trait renders as soon as you pass it.

```tsx
"use client";

import {
  ComparisonCard,
  type ComparisonOption,
} from "@/components/assistant-ui/elements/comparison-card";

const traitLabels = ["Streaming", "Multi-thread", "Self-hosted"] as const;

const options: readonly ComparisonOption[] = [
  {
    id: "local",
    name: "Local runtime",
    headline: "You own the loop",
    traits: ["Streams", "Multi-thread", "Self-hosted"],
  },
  {
    id: "external",
    name: "External store",
    headline: "Your state is the source",
    traits: ["Streams", "Multi-thread", false],
  },
];

export function RuntimeChoice() {
  return (
    <ComparisonCard
      traitLabels={traitLabels}
      options={options}
      recommendedId="local"
      reason="You already hold messages in your own store, but you want the runtime to drive streaming and branching, so the local runtime costs you less wiring."
    />
  );
}

```
