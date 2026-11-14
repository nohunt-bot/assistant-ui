# Mentions  (`composer-mentions`)

Type @ to pull people and agents into the conversation, filtered as you go.

- 完整文件：`references/pages/composer-mentions.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-composer.md`

## With a runtime

Mentions and slash commands share one trigger system, keyed by which character opens the popover; see [Slash commands](/elements/composer-slash-commands) for the mechanism itself. Mentions use the `@` char and the `Directive` behavior, which inserts text rather than firing a handler.

```tsx
"use client";

import { unstable_useMentionAdapter } from "@assistant-ui/react";

export function useComposerMentions() {
  return unstable_useMentionAdapter({
    items: [
      { id: "ada", type: "person", label: "Ada Lovelace" },
      { id: "grace", type: "person", label: "Grace Hopper" },
    ],
  });
}

```

## Standalone (no runtime)

Standalone, nothing detects the `@` for you. `useMentionMatches` filters a person list against text ending in a trailing `@query`, and `applyMention` replaces that trailing query with the chosen name once picked.

```tsx
"use client";

import { useState } from "react";
import { useMentionMatches, applyMention, ComposerMenu, ComposerPersonItem, type ComposerPerson } from "@/components/assistant-ui/elements/composer";

const people: ComposerPerson[] = [
  { name: "Ada", role: "human" },
  { name: "Grace", role: "human" },
  { name: "Reviewer", role: "agent" },
];

export function ChatBox() {
  const [text, setText] = useState("");
  const matches = useMentionMatches(text, people);

  return (
    <div className="relative">
      <ComposerMenu open={matches.length > 0}>
        {matches.map((person) => (
          <ComposerPersonItem
            key={person.name}
            person={person}
            active={false}
            onClick={() => setText((current) => applyMention(current, person.name))}
          />
        ))}
      </ComposerMenu>
      {/* ComposerInput below, controlled by the same text state */}
    </div>
  );
}

```
