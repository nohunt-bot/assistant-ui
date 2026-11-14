# Stopped run  (`stopped-run`)

You pressed stop. The half-written answer stays, and continuing is one tap away.

- 完整文件：`references/pages/stopped-run.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-stopped-run.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-stopped-run.md`

## With a runtime

A cancelled message settles into `status.type === "incomplete"` with `status.reason === "cancelled"`. Its content up to that point is still there; only the run stopped.

```tsx
"use client";

import { useMemo } from "react";
import { useAui, useAuiState } from "@assistant-ui/react";
import { StoppedRun } from "@/components/assistant-ui/elements/stopped-run";

function CancelledNotice() {
  const aui = useAui();
  const status = useAuiState((s) =>
    s.message.role === "assistant" ? s.message.status : undefined,
  );
  const content = useAuiState((s) => s.message.content);
  const words = useMemo(
    () =>
      content
        .filter((part): part is { type: "text"; text: string } => part.type === "text")
        .flatMap((part) => part.text.split(" ")),
    [content],
  );

  if (status?.type !== "incomplete" || status.reason !== "cancelled") return null;

  return (
    <StoppedRun
      words={words}
      reason="stopped by you"
      onContinue={() => aui.message.reload()}
      onDiscard={() => aui.message.delete()}
    />
  );
}

```

## Standalone (no runtime)

Standalone, StoppedRun is presentational: you decide what counts as stopped and supply the words already streamed.

```tsx
"use client";

import { useState } from "react";
import { StoppedRun } from "@/components/assistant-ui/elements/stopped-run";

export function Answer() {
  const [words, setWords] = useState<string[]>([]);
  const [stopped, setStopped] = useState(false);

  function onStop() {
    controller.abort();
    setStopped(true);
  }

  if (!stopped) return null;

  return (
    <StoppedRun
      words={words}
      reason="stopped by you"
      onContinue={() => resume(words)}
      onDiscard={() => setStopped(false)}
    />
  );
}

```
