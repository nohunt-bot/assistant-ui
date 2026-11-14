# Math  (`math-block`)

Rendered expressions with the working shown, one step at a time.

- 完整文件：`references/pages/math-block.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-math-block.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-math-block.md`

## With a runtime

This element has no assistant-ui primitive of its own: a derivation is exactly the kind of structured content a tool call carries, so the runtime wiring is a tool renderer rather than a primitive composition.

```tsx
"use client";

import { defineToolkit } from "@assistant-ui/react";
import { MathBlock } from "@/components/assistant-ui/elements/math-block";

export const toolkit = defineToolkit({
  show_derivation: {
    type: "backend",
    render: ({ args }) => {
      const steps = args.steps ?? [];
      return (
        <MathBlock
          label={args.label}
          steps={steps}
          visibleSteps={steps.length}
        />
      );
    },
  },
});

```

## Standalone (no runtime)

Standalone, the element is a plain display component: it owns no state of its own, so the minimal usage is a constant array shown all at once.

```tsx
"use client";

import {
  MathBlock,
  type MathStep,
} from "@/components/assistant-ui/elements/math-block";

const steps: readonly MathStep[] = [
  { expression: "p′(x) = p(x)·(1 − p(x))", note: "the derivative" },
  {
    expression: "max p′(x) = 0.25 at x = 0",
    note: "steepest at the midpoint",
  },
];

export function Derivation() {
  return (
    <MathBlock label="derivation" steps={steps} visibleSteps={steps.length} />
  );
}

```
