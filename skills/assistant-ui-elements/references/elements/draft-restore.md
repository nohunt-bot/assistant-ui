# Draft restore  (`draft-restore`)

Come back to a thread and the sentence you never sent is still waiting.

- 完整文件：`references/pages/draft-restore.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-draft-restore.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-draft-restore.md`

## With a runtime

With a runtime, `reloadMainThread()` does not keep unsent composer text by default: a runtime that lacks the in-place refetch capability has its hook remounted, which discards whatever was still being typed. Restoring a draft across that gap is app-level work built on the composer's own text state.

```tsx
"use client";

import { useAuiState } from "@assistant-ui/react";
import { useEffect, useRef } from "react";

function saveDraft(threadId: string, text: string) {
  if (text) localStorage.setItem(`draft:${threadId}`, text);
  else localStorage.removeItem(`draft:${threadId}`);
}

export function useDraftPersistence(threadId: string) {
  const text = useAuiState((s) => s.composer.text);
  const timer = useRef<ReturnType<typeof setTimeout> | undefined>(undefined);

  useEffect(() => {
    clearTimeout(timer.current);
    timer.current = setTimeout(() => saveDraft(threadId, text), 400);
    return () => clearTimeout(timer.current);
  }, [threadId, text]);
}

```

## Standalone (no runtime)

Standalone, `DraftRestore` is presentation only: it never measures time or watches an input, so `savedAt` arrives pre-formatted and `onRestore`/`onDiscard` are the only ways anything happens.

```tsx
"use client";

import { useState } from "react";
import { DraftRestore } from "@/components/assistant-ui/elements/draft-restore";

export function ThreadDraftBanner({
  onRestore,
}: {
  onRestore: (text: string) => void;
}) {
  const [draft, setDraft] = useState<string | null>(
    "Add a regression test for draft restore",
  );

  if (!draft) return null;

  return (
    <DraftRestore
      draft={draft}
      savedAt="2 minutes ago"
      onRestore={() => {
        onRestore(draft);
        setDraft(null);
      }}
      onDiscard={() => setDraft(null)}
    />
  );
}

```
