# Thread search  (`thread-search`)

History you can actually get back into: pinned first, then grouped by when.

- 完整文件：`references/pages/thread-search.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-thread-search.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-thread-search.md`

## With a runtime

A runtime keeps every thread's title, id, and recency in `s.threads.threadItems`. Mapping that into `SearchableThread[]` and selecting on it is the whole integration; there is no dedicated search primitive to compose.

```tsx
"use client";

import { useLayoutEffect, useMemo, useState } from "react";
import { useAui, useAuiState } from "@assistant-ui/react";
import {
  ThreadSearch,
  type SearchableThread,
} from "@/components/assistant-ui/elements/thread-search";

const DAY = 86_400_000;

function groupFor(date: Date | undefined, now: number) {
  if (!date) return "Earlier";
  if (date.getTime() >= now - DAY) return "Today";
  if (date.getTime() >= now - 2 * DAY) return "Yesterday";
  return "Earlier";
}

export function ThreadSearchPanel() {
  const aui = useAui();
  const items = useAuiState((s) => s.threads.threadItems);
  const openId = useAuiState((s) => s.threads.mainThreadId);
  const [query, setQuery] = useState("");
  const [activeId, setActiveId] = useState(openId);

  useLayoutEffect(() => setActiveId(openId), [openId]);

  const threads = useMemo<SearchableThread[]>(() => {
    const now = Date.now();
    return items
      .filter((item) => item.status === "regular")
      .map((item) => ({
        id: item.id,
        title: item.title ?? "New chat",
        preview: item.isRunning ? "Running…" : "",
        group: groupFor(item.lastMessageAt, now),
        pinned: Boolean(item.custom?.["pinned"]),
      }));
  }, [items]);

  return (
    <ThreadSearch
      threads={threads}
      query={query}
      activeId={activeId}
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, filtering, pinning, and grouping are computed for you from whatever `threads` array you pass; only the state (the query and which thread is active) is yours to hold.

```tsx
"use client";

import { useState } from "react";
import { ThreadSearch } from "@/components/assistant-ui/elements/thread-search";

const threads = [
  { id: "1", title: "Migration plan", group: "Today", preview: "Three tables, one rollback path", pinned: true },
  { id: "2", title: "Release notes draft", group: "Today", preview: "Bumped the changelog for 4.2" },
  { id: "3", title: "Onboarding copy", group: "Yesterday", preview: "First run, three moves" },
];

export function History() {
  const [query, setQuery] = useState("");
  const [activeId, setActiveId] = useState(threads[0]!.id);
  const [openId, setOpenId] = useState(threads[0]!.id);
  const openThread = threads.find((thread) => thread.id === openId);

  return (
    <>
      <ThreadSearch
        threads={threads}
        query={query}
        activeId={activeId}
        onQueryChange={setQuery}
        onActiveChange={setActiveId}
        onSelect={setOpenId}
      />
      <p>Open thread: {openThread?.title}</p>
    </>
  );
}

```
