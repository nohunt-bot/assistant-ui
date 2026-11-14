# Tool group  (`tool-group`)

A collapsible runtime wrapper around consecutive tool calls in one assistant turn.

- 完整文件：`references/pages/tool-group.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/tool-group.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-tool-group.md`

## With a runtime

`Thread` already wires this in for you.

```tsx
import { groupPartByType, MessagePrimitive } from "@assistant-ui/react";
import {
  ToolGroupContent,
  ToolGroupRoot,
  ToolGroupTrigger,
} from "@/components/assistant-ui/elements/tool-group.aui";

<MessagePrimitive.GroupedParts
  groupBy={groupPartByType({
    "tool-call": ["group-tool"],
  })}
>
  {({ part, children }) => {
    switch (part.type) {
      case "group-tool":
        return (
          <ToolGroupRoot variant="ghost">
            <ToolGroupTrigger
              count={part.indices.length}
              active={part.status.type === "running"}
            />
            <ToolGroupContent>{children}</ToolGroupContent>
          </ToolGroupRoot>
        );
      // ...other cases
    }
  }}
</MessagePrimitive.GroupedParts>

```

## Standalone (no runtime)

Standalone, there's no run to group calls from. See [The parallel-tools design](#the-parallel-tools-design) below for the props-driven version you group and control yourself.
