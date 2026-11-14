# Canvas  (`canvas-split`)

The thread steps aside and the document takes the room, still being written as you read.

- 完整文件：`references/pages/canvas-split.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-canvas-split.md`

## With a runtime

The document comes from a tool call whose arguments stream in as the model writes it. Register a renderer for it through `MessagePrimitive.Parts`:

```tsx
"use client";

import { MessagePrimitive, useToolArgsStatus, type ToolCallMessagePartProps } from "@assistant-ui/react";
import { CanvasSplitDocument, CanvasSplitHeader, CanvasSplitBody, CanvasSplitLine } from "./canvas-split";

type DocArgs = { title: string; content: string };

function DocumentTool({ args, status }: ToolCallMessagePartProps<DocArgs>) {
  const { propStatus } = useToolArgsStatus<DocArgs>();
  return (
    <CanvasSplitDocument>
      <CanvasSplitHeader
        title={args.title ?? "Untitled"}
        version={1}
        saved={status.type === "complete"}
      />
      <CanvasSplitBody writing={propStatus.content === "streaming"}>
        <CanvasSplitLine>{args.content}</CanvasSplitLine>
      </CanvasSplitBody>
    </CanvasSplitDocument>
  );
}

<MessagePrimitive.Parts components={{ tools: { by_name: { write_document: DocumentTool } } }} />

```

## Standalone (no runtime)

Standalone, the thread half and the document half are both yours to drive; nothing ties them together beyond sharing one `CanvasSplit` row.

```tsx
"use client";

import {
  CanvasSplit,
  CanvasSplitThread,
  CanvasSplitMessage,
  CanvasSplitDocument,
  CanvasSplitHeader,
  CanvasSplitBody,
  CanvasSplitLine,
} from "@/components/assistant-ui/elements/canvas-split";

export function Canvas() {
  return (
    <CanvasSplit>
      <CanvasSplitThread>
        <CanvasSplitMessage speaker="user">Draft a project outline.</CanvasSplitMessage>
        <CanvasSplitMessage speaker="assistant">Here's a first pass, on the right.</CanvasSplitMessage>
      </CanvasSplitThread>
      <CanvasSplitDocument>
        <CanvasSplitHeader title="Project outline" version={1} saved onCopy={() => {}} onClose={() => {}} />
        <CanvasSplitBody>
          <CanvasSplitLine heading>Goals</CanvasSplitLine>
          <CanvasSplitLine>Ship the first milestone by Friday.</CanvasSplitLine>
        </CanvasSplitBody>
      </CanvasSplitDocument>
    </CanvasSplit>
  );
}

```
