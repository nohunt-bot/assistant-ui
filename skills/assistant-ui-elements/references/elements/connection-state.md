# Connection state  (`connection-state`)

The socket drops, the run keeps going on the server, and the stream is picked back up.

- 完整文件：`references/pages/connection-state.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-connection-state.md`

## With a runtime

assistant-ui doesn't expose a single connection field on thread state, because a dropped socket is something an adapter observes, not a concept the runtime itself models. What it exposes instead is a resumable run: catch the failure where your adapter reports it, show the banner, then call `resumeRun` to pick the same run back up.

```tsx
"use client";

import { useEffect, useState } from "react";
import { useAui, useAuiState } from "@assistant-ui/react";
import { ConnectionState, type ConnectionPhase } from "./connection-state";

function ConnectionBanner({ resumeFromId }: { resumeFromId: string | null }) {
  const aui = useAui();
  const isRunning = useAuiState((s) => s.thread.isRunning);
  const [phase, setPhase] = useState<ConnectionPhase>("online");

  useEffect(() => {
    if (phase === "reconnecting" && isRunning) setPhase("resumed");
  }, [phase, isRunning]);

  return (
    <ConnectionState
      phase={phase}
      onRetry={() => {
        setPhase("reconnecting");
        aui.thread.resumeRun({ parentId: resumeFromId });
      }}
    />
  );
}

```

## Standalone (no runtime)

Standalone, `ConnectionState` is a pure display: you own the phase and it renders the matching banner, or nothing at all.

```tsx
"use client";

import { useState } from "react";
import { ConnectionState, type ConnectionPhase } from "@/components/assistant-ui/elements/connection-state";

export function Banner() {
  const [phase, setPhase] = useState<ConnectionPhase>("online");
  return (
    <ConnectionState
      phase={phase}
      attempt={phase === "reconnecting" ? 1 : undefined}
      onRetry={() => setPhase("reconnecting")}
    />
  );
}

```
