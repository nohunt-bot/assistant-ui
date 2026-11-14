# Web search  (`web-search`)

A search query and its results landing one by one as the agent reads.

- 完整文件：`references/pages/web-search.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/thread.md`、`source/<flavor>/aui/elements-web-search.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-web-search.md`

## With a runtime

A search tool renders through that tool's own toolkit entry: the model's arguments arrive as `args`, the provider's results as `result`, and the call's lifecycle as `status`.

```tsx
"use generative";

import { defineToolkit, externalTool } from "@assistant-ui/react";
import { WebSearch } from "@/components/assistant-ui/elements/web-search";

export default defineToolkit({
  web_search: {
    execute: externalTool(),
    render: ({ args, result, isError, status }) =>
      isError || status.type === "incomplete" ? null : (
        <WebSearch
          query={args.query ?? ""}
          results={result?.results ?? []}
          visibleResults={result?.results.length ?? 0}
          searching={status.type === "running" || status.type === "requires-action"}
          cycle={0}
        />
      ),
  },
});

```

## Standalone (no runtime)

Standalone, `WebSearch` is fully controlled: you hold the query, the full result list, and a count of how many of them to reveal.

```tsx
"use client";

import { useState } from "react";
import {
  WebSearch,
  type WebSearchResult,
} from "@/components/assistant-ui/elements/web-search";

const RESULTS: readonly WebSearchResult[] = [
  { title: "Persisting composer state across threads", domain: "assistant-ui.com" },
  { title: "Draft autosave patterns in chat UIs", domain: "patterns.dev" },
];

export function Search() {
  const [searching, setSearching] = useState(true);
  const [visibleResults, setVisibleResults] = useState(0);

  return (
    <WebSearch
      query="assistant-ui draft persistence"
      results={RESULTS}
      visibleResults={visibleResults}
      searching={searching}
      cycle={0}
    />
  );
}

```
