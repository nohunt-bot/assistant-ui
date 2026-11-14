# Prompt library  (`prompt-library`)

Prompts you saved, searchable, with their variables shown before you insert one.

- 完整文件：`references/pages/prompt-library.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-prompt-library.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-prompt-library.md`

## With a runtime

assistant-ui does not store saved prompts, so there is no library to read from the runtime. The catalog, its storage, and its search are yours, as in the standalone lane. The runtime is involved only when a prompt is inserted.

```tsx
"use client";

import { useAui } from "@assistant-ui/react";
import { useState } from "react";
import {
  PromptLibrary,
  type SavedPrompt,
} from "@/components/assistant-ui/elements/prompt-library";

export function PromptLibraryPanel({
  prompts,
}: {
  prompts: readonly SavedPrompt[];
}) {
  const aui = useAui();
  const [query, setQuery] = useState("");
  const [selectedId, setSelectedId] = useState(prompts[0]?.id ?? "");

  return (
    <PromptLibrary
      prompts={prompts}
      query={query}
      selectedId={selectedId}
      onQueryChange={setQuery}
      onSelect={setSelectedId}
      onInsert={(id) => {
        const prompt = prompts.find((p) => p.id === id);
        if (prompt) aui.composer.setText(prompt.body);
      }}
    />
  );
}

```

## Standalone (no runtime)

Standalone, `PromptLibrary` filters, previews, and handles its own arrow-key and Enter navigation; you hold the list, the query, and the selection, and decide what `onInsert` actually does.

```tsx
"use client";

import { useState } from "react";
import { PromptLibrary } from "@/components/assistant-ui/elements/prompt-library";

const prompts = [
  {
    id: "bug-report",
    name: "Bug report",
    body: "Describe the bug, the steps to reproduce it, and the expected behavior.",
    variables: [],
  },
  {
    id: "summarize",
    name: "Summarize {topic}",
    body: "Summarize {topic} in three sentences for a non-technical reader.",
    variables: ["topic"],
  },
];

export function PromptLibraryPanel({
  onInsert,
}: {
  onInsert: (text: string) => void;
}) {
  const [query, setQuery] = useState("");
  const [selectedId, setSelectedId] = useState(prompts[0].id);

  return (
    <PromptLibrary
      prompts={prompts}
      query={query}
      selectedId={selectedId}
      onQueryChange={setQuery}
      onSelect={setSelectedId}
      onInsert={(id) => {
        const prompt = prompts.find((p) => p.id === id);
        if (prompt) onInsert(prompt.body);
      }}
    />
  );
}

```
