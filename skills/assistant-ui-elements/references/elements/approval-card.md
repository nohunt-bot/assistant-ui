# Approval card  (`approval-card`)

Human in the loop: the agent asks before it takes a consequential action.

- 完整文件：`references/pages/approval-card.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-approval-card.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-approval-card.md`

## With a runtime

A server-side tool approval gate keeps the request open until the user answers. Map the tool arguments to the card and send the selected response through `respondToApproval`.

```tsx
"use client";

import { Trash2Icon } from "lucide-react";
import { defineToolkit, type ToolApprovalResponse } from "@assistant-ui/react";
import { ApprovalCard } from "@/components/assistant-ui/elements/approval-card";

export const toolkit = defineToolkit({
  manage_records: {
    type: "backend",
    render: ({ args, approval, respondToApproval, result }) => {
      const answer = async (response: ToolApprovalResponse) => {
        try {
          await respondToApproval(response);
        } catch (failure) {
          console.error(failure);
        }
      };
      const destructive = args.action === "delete";

      return (
        <ApprovalCard
          state={
            approval?.approved === false
              ? "denied"
              : approval?.approved === undefined
                ? "request"
                : result === undefined
                  ? "running"
                  : "done"
          }
          command={args.command}
          title={args.title}
          subtitle={args.subtitle}
          description={args.description}
          details={args.details}
          variant={destructive ? "destructive" : "default"}
          icon={destructive ? <Trash2Icon className="size-4" /> : undefined}
          onAllowOnce={() => void answer({ optionId: "once" })}
          onAlwaysAllow={() => void answer({ optionId: "always" })}
          onDeny={() => void answer({ optionId: "deny" })}
        />
      );
    },
  },
});
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, the element is a controlled display: you own `state` and decide what each button does.

```tsx
"use client";

import { useState } from "react";
import {
  ApprovalCard,
  type ApprovalState,
} from "@/components/assistant-ui/elements/approval-card";

export function Approval() {
  const [state, setState] = useState<ApprovalState>("request");

  return (
    <ApprovalCard
      state={state}
      command="pnpm vitest run --changed"
      title="Run checks"
      subtitle="The agent wants to run a shell command"
      description="This runs the tests affected by the current changes."
      onAllowOnce={() => setState("running")}
      onAlwaysAllow={() => setState("running")}
      onDeny={() => setState("denied")}
    />
  );
}

```
