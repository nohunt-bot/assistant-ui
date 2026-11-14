# Loader  (`loading-state`)

A pixel matrix that keeps time while the model has nothing to show yet.

- 完整文件：`references/pages/loading-state.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-loading-state.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-loading-state.md`

## With a runtime

The runtime does not tick a clock or provide a status string. It does tell you when the loader belongs on screen: a run is active and the newest message has no parts yet.

```tsx
"use client";

import { useAuiState } from "@assistant-ui/react";

function useIsAwaitingFirstToken() {
  return useAuiState((s) => {
    if (!s.thread.isRunning) return false;
    const last = s.thread.messages.at(-1);
    return last?.role === "assistant" && last.parts.length === 0;
  });
}

```

## Standalone (no runtime)

Standalone, both `label` and `tick` are yours: the component only turns a tick number into a moving band of lit cells and prints the label underneath.

```tsx
"use client";

import { useEffect, useState } from "react";
import { GenerationLoader } from "@/components/assistant-ui/elements/loading-state";

export function Loader({ label }: { label: string }) {
  const [tick, setTick] = useState(0);

  useEffect(() => {
    const id = setInterval(() => setTick((t) => t + 1), 120);
    return () => clearInterval(id);
  }, []);

  return <GenerationLoader label={label} tick={tick} />;
}

```
