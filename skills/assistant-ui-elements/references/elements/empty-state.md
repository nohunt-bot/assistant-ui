# Empty state  (`empty-state`)

The first screen: a greeting, three ways in, and the composer front and center.

- 完整文件：`references/pages/empty-state.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-empty-state.md`

## With a runtime

An assistant-ui thread starts empty until the first message lands, and `s.thread.isEmpty` tracks exactly that.

```tsx
"use client";

import { AuiIf } from "@assistant-ui/react";
import { EmptyState, EmptyStateGreeting } from "./empty-state";

function Welcome() {
  return (
    <AuiIf condition={(s) => s.thread.isEmpty}>
      <EmptyState>
        <EmptyStateGreeting>How can I help?</EmptyStateGreeting>
        {/* suggestions, composer */}
      </EmptyState>
    </AuiIf>
  );
}

```

## Standalone (no runtime)

Standalone, the whole screen is yours: greeting text, the suggestion list, and what pressing one does.

```tsx
"use client";

import {
  EmptyState,
  EmptyStateGreeting,
  EmptyStateSuggestions,
  EmptyStateSuggestion,
  EmptyStateComposer,
} from "@/components/assistant-ui/elements/empty-state";

const STARTERS = ["Summarize this doc", "Draft a reply", "Explain this error"];

export function Welcome({ onPick }: { onPick: (prompt: string) => void }) {
  return (
    <EmptyState>
      <EmptyStateGreeting>How can I help?</EmptyStateGreeting>
      <EmptyStateSuggestions>
        {STARTERS.map((prompt, index) => (
          <EmptyStateSuggestion key={prompt} index={index} onClick={() => onPick(prompt)}>
            {prompt}
          </EmptyStateSuggestion>
        ))}
      </EmptyStateSuggestions>
      <EmptyStateComposer placeholder="Ask anything" onSend={() => onPick("")} />
    </EmptyState>
  );
}

```
