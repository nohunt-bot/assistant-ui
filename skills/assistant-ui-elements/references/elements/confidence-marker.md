# Confidence  (`confidence-marker`)

Which claims came from a source, which were inferred, and which are guesses.

- 完整文件：`references/pages/confidence-marker.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-confidence-marker.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-confidence-marker.md`

## With a runtime

assistant-ui's message content has no built-in per-claim confidence concept: text and citations are separate content parts, and neither carries a confidence grade. The closest real building block is a tool call. Ask the model to return its answer as graded claims, and read the result back off the `tool-call` part.

```tsx
"use client";

import { useState } from "react";
import { useAuiState } from "@assistant-ui/react";
import type { ToolCallMessagePart } from "@assistant-ui/react";
import {
  ConfidenceMarker,
  type ConfidenceClaim,
} from "@/components/assistant-ui/elements/confidence-marker";

type CiteClaims = ToolCallMessagePart<unknown, { claims: ConfidenceClaim[] }>;

function ClaimsFromMessage() {
  const [hoveredId, setHoveredId] = useState("");
  const claims = useAuiState((s) =>
    s.message.role === "assistant"
      ? s.message.content.find(
          (part): part is CiteClaims =>
            part.type === "tool-call" && part.toolName === "cite_claims",
        )?.result?.claims
      : undefined,
  );

  if (!claims) return null;

  return <ConfidenceMarker claims={claims} hoveredId={hoveredId} onHover={setHoveredId} />;
}

```

## Standalone (no runtime)

Standalone, you already hold the claim list; the component only tracks which one is hovered or focused.

```tsx
"use client";

import { useState } from "react";
import {
  ConfidenceMarker,
  type ConfidenceClaim,
} from "@/components/assistant-ui/elements/confidence-marker";

const CLAIMS: ConfidenceClaim[] = [
  { id: "1", text: "The composer owns its draft from 0.14 onward.", confidence: "grounded", basis: "migration-0.14.md" },
  { id: "2", text: "Most apps will not need the hydrate effect.", confidence: "inferred", basis: "from the changed API" },
];

export function Answer() {
  const [hoveredId, setHoveredId] = useState("");
  return <ConfidenceMarker claims={CLAIMS} hoveredId={hoveredId} onHover={setHoveredId} />;
}

```
