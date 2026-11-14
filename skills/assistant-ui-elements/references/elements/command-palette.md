# Command palette  (`command-palette`)

Everything the app can do, one keystroke away and grouped by where it acts.

- 完整文件：`references/pages/command-palette.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-command-palette.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-command-palette.md`

## With a runtime

With a runtime, there is no registry of "everything the app can do" to read: assistant-ui exposes actions on the scopes you already work with (a thread, a message, the thread list), not a flat catalog of commands. Build the list yourself, grouped however your app is organized, and give each entry an id you can route in `onRun`.

```tsx
"use client";

import { useAui } from "@assistant-ui/react";
import { useState } from "react";
import {
  CommandPalette,
  type PaletteCommand,
} from "@/components/assistant-ui/elements/command-palette";

const commands: PaletteCommand[] = [
  { id: "new-thread", label: "New thread", group: "Thread list", keys: ["⌘", "N"] },
  { id: "stop", label: "Stop generating", group: "Thread", keys: ["Esc"] },
  { id: "archive", label: "Archive this thread", group: "Thread", keys: ["⌘", "⇧", "A"] },
];

export function AppCommandPalette() {
  const aui = useAui();
  const [query, setQuery] = useState("");
  const [activeId, setActiveId] = useState(commands[0]!.id);

  const run = (id: string) => {
    if (id === "new-thread") aui.threads.switchToNewThread();
    if (id === "stop") aui.thread.cancelRun();
    if (id === "archive") aui.threadListItem.archive();
  };

  return (
    <CommandPalette
      commands={commands}
      query={query}
      activeId={activeId}
      onQueryChange={setQuery}
      onActiveChange={setActiveId}
      onRun={run}
    />
  );
}

```

## Standalone (no runtime)

Standalone, `CommandPalette` filters and groups commands and handles the keyboard events. Arrow keys move the active id only through `onActiveChange`, and Enter runs the active command only through `onRun`; you hold that state and decide what each id does.

```tsx
"use client";

import { useState } from "react";
import { CommandPalette } from "@/components/assistant-ui/elements/command-palette";

const commands = [
  { id: "new-doc", label: "New document", group: "File", keys: ["⌘", "N"] },
  { id: "share", label: "Share", group: "File", keys: ["⌘", "⇧", "S"] },
];

export function AppCommandPalette({
  handlers,
}: {
  handlers: Record<string, () => void>;
}) {
  const [query, setQuery] = useState("");
  const [activeId, setActiveId] = useState(commands[0].id);

  return (
    <CommandPalette
      commands={commands}
      query={query}
      activeId={activeId}
      onQueryChange={setQuery}
      onActiveChange={setActiveId}
      onRun={(id) => handlers[id]?.()}
    />
  );
}

```
