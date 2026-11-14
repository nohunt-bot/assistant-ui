# Context  (`composer-context`)

A token ring in the rail fills as the conversation grows, warning near the limit.

- 完整文件：`references/pages/composer-context.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-composer.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-composer.md`

## With a runtime

With a runtime, assistant-ui does not track a system, tools, and messages split on its own. What it does track is per-turn usage: a `ChatModelAdapter` can report `inputTokens` and `outputTokens` for each generation step, and the thread carries that on every assistant message.

```tsx
"use client";

import { useAuiState } from "@assistant-ui/react";

function useThreadTokensUsed(): number {
  return useAuiState((s) =>
    s.thread.messages.reduce((sum, message) => {
      const steps = message.metadata.steps ?? [];
      return (
        sum +
        steps.reduce(
          (stepSum, step) =>
            stepSum +
            (step.usage?.inputTokens ?? 0) +
            (step.usage?.outputTokens ?? 0),
          0,
        )
      );
    }, 0),
  );
}

```

## Standalone (no runtime)

Standalone, `ComposerContext` is fully controlled by its one `usage` prop. Nothing in the element measures tokens; you own that math and pass the result in.

```tsx
"use client";

import { ComposerContext } from "@/components/assistant-ui/elements/composer";

export function ComposerRail() {
  return (
    <ComposerContext usage={{ system: 12, tools: 8, messages: 54, total: 200 }} />
  );
}

```
