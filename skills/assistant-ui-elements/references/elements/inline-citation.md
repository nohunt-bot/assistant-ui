# Inline citation  (`inline-citation`)

A compact, numbered source marker you place beside any claim in prose.

- 完整文件：`references/pages/inline-citation.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-inline-citation.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-inline-citation.md`

## With a runtime

When a runtime receives source parts or a tool result, map each source to a `Citation` at the position it belongs in the rendered answer. The element does not infer offsets from streamed text.

```tsx
"use client";

import {
  Citation,
  InlineCitation,
  type Source,
} from "@/components/assistant-ui/elements/inline-citation";

type AnswerPart =
  | { type: "text"; text: string }
  | { type: "source"; id: string; index: number; source: Source };

export function Answer({ parts }: { parts: readonly AnswerPart[] }) {
  return (
    <InlineCitation>
      {parts.map((part) =>
        part.type === "text" ? (
          part.text
        ) : (
          <Citation key={part.id} index={part.index} source={part.source} />
        ),
      )}
    </InlineCitation>
  );
}

```

## Standalone (no runtime)

Standalone, place a marker directly inside the prose. Omit `open` for the built-in hover and focus behavior.

```tsx
import {
  Citation,
  InlineCitation,
  type Source,
} from "@/components/assistant-ui/elements/inline-citation";

const RUNTIME_SOURCE: Source = {
  title: "Optimistic updates in the runtime",
  snippet:
    "The runtime applies local edits immediately and reconciles them once the server acknowledges the write.",
  url: "https://www.assistant-ui.com",
  publishedAt: "2025-03-01T12:00:00.000Z",
};

export function Answer() {
  return (
    <InlineCitation>
      Optimistic updates keep the thread responsive
      <Citation index={0} source={RUNTIME_SOURCE} />.
    </InlineCitation>
  );
}

```
