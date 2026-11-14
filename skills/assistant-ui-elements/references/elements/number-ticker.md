# Number ticker  (`number-ticker`)

Digits that roll into place as a count updates in real time.

- 完整文件：`references/pages/number-ticker.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-number-ticker.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-number-ticker.md`

## With a runtime

With a runtime, the ticker only needs a number and a label, so its renderer is one of the smallest in the catalog: read `result` from the tool call, and fall back to zero while it is still running.

```tsx
"use client";

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import { NumberTicker } from "@/components/assistant-ui/elements/number-ticker";

export const TokenCountToolUI: ToolCallMessagePartComponent<
  Record<string, never>,
  { total: number }
> = ({ result, status }) => (
  <NumberTicker
    value={status.type === "complete" ? (result?.total ?? 0) : 0}
    label="tokens generated"
  />
);

```

## Standalone (no runtime)

Standalone, the ticker is fully controlled: it re-renders and rolls whenever `value` changes, from whatever source you like.

```tsx
"use client";

import { useState } from "react";
import { NumberTicker } from "@/components/assistant-ui/elements/number-ticker";

export function TokenCount() {
  const [value, setValue] = useState(0);
  return <NumberTicker value={value} label="tokens generated" />;
}

```
