# Reasoning  (`reasoning`)

A collapsible renderer for assistant reasoning that follows the active message part.

- 完整文件：`references/pages/reasoning.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/reasoning.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-reasoning.md`
- Thread 內建：The `Thread` element already ships this composition, so installing `@assistant-ui/thread` gives you the reasoning trace with none of the above (see [Thread](/elements/thread)).

## With a runtime

A runtime streams `reasoning` message parts alongside the rest of a message. Consecutive ones group into a single `group-reasoning` part, and that group is what the disclosure wraps.

```tsx
import { MessagePrimitive, groupPartByType } from "@assistant-ui/react";
import {
  ReasoningContent,
  ReasoningRoot,
  ReasoningText,
  ReasoningTrigger,
} from "@/components/assistant-ui/elements/reasoning.aui";

<MessagePrimitive.GroupedParts
  groupBy={groupPartByType({ reasoning: ["group-reasoning"] })}
>
  {({ part, children }) => {
    if (part.type !== "group-reasoning") return null;
    const running = part.status.type === "running";
    return (
      <ReasoningRoot streaming={running}>
        <ReasoningTrigger active={running} />
        <ReasoningContent aria-busy={running}>
          <ReasoningText>{children}</ReasoningText>
        </ReasoningContent>
      </ReasoningRoot>
    );
  }}
</MessagePrimitive.GroupedParts>

```

## Standalone (no runtime)

Standalone, the element is disclosure UI over content you already have. It takes text and a streaming flag, with no message parts or grouping.

```tsx
"use client";

import {
  ReasoningContent,
  ReasoningRoot,
  ReasoningText,
  ReasoningTrigger,
} from "@/components/assistant-ui/elements/reasoning";

export function Trace({
  text,
  streaming,
}: {
  text: string;
  streaming: boolean;
}) {
  return (
    <ReasoningRoot streaming={streaming}>
      <ReasoningTrigger active={streaming} />
      <ReasoningContent aria-busy={streaming}>
        <ReasoningText>{text}</ReasoningText>
      </ReasoningContent>
    </ReasoningRoot>
  );
}

```
