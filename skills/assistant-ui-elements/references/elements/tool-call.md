# Tool call  (`tool-call`)

One tool invocation with its request and result tucked behind a disclosure.

- 完整文件：`references/pages/tool-call.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-tool-call.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-tool-call.md`

## With a runtime

A tool call renders through a toolkit entry's `render` field, not through a standalone primitive tree. The renderer receives the live `args`, `argsText`, `result`, and `status` for that invocation.

```tsx
"use client";

import { useState } from "react";
import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import { ToolCall } from "@/components/assistant-ui/elements/tool-call";

export type SearchDocsResult = { count: number; bestPath: string };

export const SearchDocsToolUI: ToolCallMessagePartComponent<
  { query: string },
  SearchDocsResult
> = ({ args, argsText, result, isError, status }) => {
  const [open, setOpen] = useState(false);
  return (
    <ToolCall
      label="Searched the docs"
      activeLabel="Searching the docs"
      query={args.query ?? ""}
      request={argsText}
      result={
        status.type === "incomplete"
          ? String(status.error ?? "")
          : isError
            ? typeof result === "string" ? result : JSON.stringify(result)
            : result
              ? `${result.count} matches, best hit ${result.bestPath}`
              : ""
      }
      running={status.type === "running"}
      failed={isError === true || (status.type === "incomplete" && status.reason === "error")}
      open={open}
      onOpenChange={setOpen}
    />
  );
};

```

## Standalone (no runtime)

Standalone, the element is fully controlled: every string it shows and its open state are props you own.

```tsx
"use client";

import { useState } from "react";
import { ToolCall } from "@/components/assistant-ui/elements/tool-call";

export function DocsSearchCall() {
  const [open, setOpen] = useState(false);

  return (
    <ToolCall
      label="Searched the docs"
      activeLabel="Searching the docs"
      query="draft persistence"
      request='{"query": "draft persistence"}'
      result="3 matches, best hit /docs/runtime/drafts"
      running={false}
      open={open}
      onOpenChange={setOpen}
    />
  );
}

```
