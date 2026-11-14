# Search in conversation  (`conversation-search`)

Find inside a long thread, with every hit marked down the scrollbar.

- 完整文件：`references/pages/conversation-search.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-conversation-search.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-conversation-search.md`

## With a runtime

There is no search primitive in the runtime; a thread only exposes its messages. Reading `s.thread.messages` and scanning each message's text parts gives you the same `hits` array the standalone element expects, so you can drive it from real conversation data with no server round trip.

```tsx
"use client";

import { useMemo, useState } from "react";
import { useAuiState } from "@assistant-ui/react";
import {
  ConversationSearch,
  type SearchHit,
} from "@/components/assistant-ui/elements/conversation-search";

const CONTEXT = 24;

export function ThreadSearchBar() {
  const messages = useAuiState((s) => s.thread.messages);
  const [query, setQuery] = useState("");
  const [activeIndex, setActiveIndex] = useState(0);

  const hits = useMemo<SearchHit[]>(() => {
    if (query === "") return [];
    const needle = query.toLowerCase();
    const found: SearchHit[] = [];

    messages.forEach((message, messageIndex) => {
      const text = message.parts
        .filter((part) => part.type === "text")
        .map((part) => part.text)
        .join(" ");
      const haystack = text.toLowerCase();

      let from = 0;
      let at: number;
      while ((at = haystack.indexOf(needle, from)) !== -1) {
        found.push({
          id: `${message.id}-${at}`,
          before: text.slice(Math.max(0, at - CONTEXT), at),
          match: text.slice(at, at + query.length),
          after: text.slice(at + query.length, at + query.length + CONTEXT),
          position: (messageIndex / Math.max(1, messages.length - 1)) * 100,
        });
        from = at + query.length;
      }
    });

    return found;
  }, [messages, query]);

// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, the element does no matching of its own: you own the query, compute the `hits`, and track which one is active.

```tsx
"use client";

import { useMemo, useState } from "react";
import {
  ConversationSearch,
  type SearchHit,
} from "@/components/assistant-ui/elements/conversation-search";

const lines = [
  "The migration touches three tables.",
  "Rollback drops the new columns and restores the index.",
  "The index rebuild takes about four minutes on staging.",
];

export function Transcript() {
  const [query, setQuery] = useState("");
  const [activeIndex, setActiveIndex] = useState(0);

  const hits = useMemo<SearchHit[]>(() => {
    if (query === "") return [];
    const needle = query.toLowerCase();
    return lines
      .map((line, i) => ({ line, i, at: line.toLowerCase().indexOf(needle) }))
      .filter(({ at }) => at !== -1)
      .map(({ line, i, at }) => ({
        id: `${i}`,
        before: line.slice(0, at),
        match: line.slice(at, at + query.length),
        after: line.slice(at + query.length),
        position: (i / (lines.length - 1)) * 100,
      }));
  }, [query]);

  return (
    <ConversationSearch
      query={query}
      hits={hits}
      activeIndex={activeIndex}
      onQueryChange={(next) => {
        setQuery(next);
        setActiveIndex(0);
      }}
      onStep={(delta) =>
        setActiveIndex((i) => (hits.length === 0 ? 0 : (i + delta + hits.length) % hits.length))
      }
// …（截斷，完整見原始頁）
```
