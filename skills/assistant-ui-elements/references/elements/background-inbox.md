# Background runs  (`background-inbox`)

Work still going somewhere else, and the results waiting to be collected.

- 完整文件：`references/pages/background-inbox.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-background-inbox.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-background-inbox.md`

## With a runtime

Every assistant-ui thread can keep running after you switch away from it. The thread list already tracks which ones are still active, so the inbox is a small projection of that state rather than a separate subsystem.

```tsx
"use client";

import { useAui, useAuiState } from "@assistant-ui/react";
import type { BackgroundRun } from "@/components/assistant-ui/elements/background-inbox";

function formatElapsed(date: Date | undefined) {
  if (!date) return "";
  const minutes = Math.round((Date.now() - date.getTime()) / 60000);
  return minutes < 1 ? "just now" : minutes < 60 ? `${minutes}m` : `${Math.round(minutes / 60)}h`;
}

function useBackgroundRuns(): BackgroundRun[] {
  const aui = useAui();
  const threadIds = useAuiState((s) => s.threads.threadIds);

  return threadIds.map((_, index) => {
    const item = aui.threads.item({ index }).getState();
    return {
      id: item.id,
      title: item.title ?? "Untitled",
      state: item.isRunning ? "running" : "ready",
      elapsed: formatElapsed(item.lastMessageAt),
    };
  });
}

```

## Standalone (no runtime)

Standalone, the element is fully controlled: you hold the array of runs and update entries as their state changes elsewhere in your app.

```tsx
"use client";

import { useState } from "react";
import { BackgroundInbox, type BackgroundRun } from "@/components/assistant-ui/elements/background-inbox";

const initialRuns: BackgroundRun[] = [
  { id: "1", title: "Refactor auth module", state: "running", elapsed: "2m" },
  { id: "2", title: "Nightly report", state: "ready", elapsed: "14m", summary: "3 files changed" },
];

export function Inbox() {
  const [runs, setRuns] = useState(initialRuns);
  return <BackgroundInbox runs={runs} onCollect={(id) => openRun(id)} />;
}

```
