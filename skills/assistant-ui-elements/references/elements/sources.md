# Sources  (`sources`)

Safe runtime citation chips and static source cards with linked reference details.

- 完整文件：`references/pages/sources.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/sources.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-sources.md`

## With a runtime

Register `Sources` as the `Source` renderer on `MessagePrimitive.Parts`. Every source part in the message routes through it automatically.

```tsx
import { Sources } from "@/components/assistant-ui/elements/sources.aui";
import { MessagePrimitive } from "@assistant-ui/react";

function AssistantMessage() {
  return (
    <MessagePrimitive.Root>
      <MessagePrimitive.Parts components={{ Source: Sources }} />
    </MessagePrimitive.Root>
  );
}
```

## Standalone (no runtime)

import { useState } from "react";
import { Sources, type Source } from "@/components/assistant-ui/elements/sources";

```tsx
"use client";

import { useState } from "react";
import { Sources, type Source } from "@/components/assistant-ui/elements/sources";

const sources: Source[] = [
  {
    title: "You might not need an effect",
    url: "https://react.dev/learn/you-might-not-need-an-effect",
    snippet: "Derive values during render before reaching for an Effect.",
    author: "React Team",
    publishedAt: "2024-10-01",
  },
];

export function Citations() {
  const [open, setOpen] = useState(false);

  return (
    <Sources
      sources={sources}
      open={open}
      onOpenChange={setOpen}
    />
  );
}
```
