# Tool failure  (`tool-error`)

One call failed. The error, the attempt count, and a retry that doesn't restart the turn.

- 完整文件：`references/pages/tool-error.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-tool-error.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-tool-error.md`

## With a runtime

A call fails in one of two ways. A tool whose `execute` throws completes with `isError` set and the error as its `result`. A run that fails before the tool returns leaves the call `"incomplete"` with `status.error`. Branch on both inside the same renderer that shows the tool's normal result.

```tsx
"use client";

import { useAui, useAuiState, type ToolCallMessagePartProps } from "@assistant-ui/react";
import { ToolError } from "@/components/assistant-ui/elements/tool-error";

export function SearchTool({ toolName, args, status, result, isError }: ToolCallMessagePartProps<{ query: string }, string>) {
  const attempt = useAuiState((s) => s.message.branchCount);
  const aui = useAui();

  const runFailed = status.type === "incomplete" && status.reason === "error";
  if (isError === true || runFailed) {
    const error = isError ? result : status.type === "incomplete" ? status.error : undefined;
    return (
      <ToolError
        name={toolName}
        target={args.query}
        message={typeof error === "string" ? error : JSON.stringify(error)}
        attempt={attempt}
        maxAttempts={3}
        retrying={false}
        onRetry={() => aui.message.reload()}
      />
    );
  }

  return <p>{result}</p>;
}

```

## Standalone (no runtime)

Standalone, there's no turn to restart: `onRetry` can call whatever produced the failure again directly, and the attempt count is yours to track.

```tsx
"use client";

import { useState } from "react";
import { ToolError } from "@/components/assistant-ui/elements/tool-error";

export function SearchFailure({ query }: { query: string }) {
  const [attempt, setAttempt] = useState(1);
  const [retrying, setRetrying] = useState(false);
  const [message, setMessage] = useState("The search API timed out.");

  return (
    <ToolError
      name="search_web"
      target={query}
      message={message}
      attempt={attempt}
      maxAttempts={3}
      retrying={retrying}
      onRetry={() => retry(query)}
    />
  );
}

```
