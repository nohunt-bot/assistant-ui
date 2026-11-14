# Slash commands  (`composer-slash-commands`)

Type a slash and the command menu floats above the input, filtering as you continue.

- 完整文件：`references/pages/composer-slash-commands.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-composer.md`

## With a runtime

assistant-ui's composer has stable primitives and types for character-activated popovers: a character like `/` opens a scoped popover with its own search, keyboard navigation, and selection, without touching the rest of the input. The `unstable_useSlashCommandAdapter` helper and trigger popover context hooks remain unstable.

```tsx
"use client";

import { unstable_useSlashCommandAdapter } from "@assistant-ui/react";

export function useComposerCommands() {
  return unstable_useSlashCommandAdapter({
    commands: [
      { id: "summarize", description: "Summarize this thread", icon: "FileText", execute: () => runSummarize() },
      { id: "translate", description: "Translate the last reply", icon: "Languages", execute: () => runTranslate() },
    ],
  });
}

```

## Standalone (no runtime)

Standalone, nothing detects the `/` for you. `useSlashMatches` filters a command list against the current draft text and returns the matches to render into a menu; you hold which one is highlighted.

```tsx
"use client";

import { useState } from "react";
import { useSlashMatches, ComposerMenu, ComposerCommandItem, type ComposerCommand } from "@/components/assistant-ui/elements/composer";
import { FileTextIcon, LanguagesIcon } from "lucide-react";

const commands: ComposerCommand[] = [
  { name: "summarize", description: "Summarize this thread", icon: FileTextIcon },
  { name: "translate", description: "Translate the last reply", icon: LanguagesIcon },
];

export function ChatBox() {
  const [text, setText] = useState("");
  const matches = useSlashMatches(text, commands);

  return (
    <div className="relative">
      <ComposerMenu open={matches.length > 0}>
        {matches.map((command) => (
          <ComposerCommandItem key={command.name} command={command} active={false} onClick={() => runCommand(command.name)} />
        ))}
      </ComposerMenu>
      {/* ComposerInput below */}
    </div>
  );
}

```
