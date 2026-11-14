# Permission grant  (`permission-grant`)

Granting a capability rather than approving one action, with the reach spelled out.

- 完整文件：`references/pages/permission-grant.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-permission-grant.md`

## With a runtime

A runtime whose backend declares tool approval gates (an AG-UI or ACP agent, for example) attaches an `approval` object to the tool-call part. Read it and answer it from the same renderer that shows the tool's normal result.

```tsx
"use client";

import type { ToolApprovalResponse, ToolCallMessagePartProps } from "@assistant-ui/react";

function ShellApprovalGate({ args, approval, respondToApproval }: ToolCallMessagePartProps<{ command: string }, string>) {
  if (!approval || approval.approved !== undefined || approval.resolution !== undefined) {
    return null;
  }

  // A refused response rejects and a precondition (an unknown option, an answer
  // the request does not take) throws, so `try`/`await` covers both and the
  // controls stay actionable.
  const answer = async (response: ToolApprovalResponse) => {
    try {
      await respondToApproval(response);
    } catch (failure) {
      console.error(failure);
    }
  };

  return (
    <div className="flex flex-col gap-3.5 rounded-[20px] border p-4">
      <p className="text-sm font-medium">Run: {args.command}</p>
      <div className="flex flex-wrap gap-2">
        {(approval.options ?? []).map((option) => (
          <button key={option.id} type="button" onClick={() => void answer({ optionId: option.id })}>
            {option.label ?? option.kind}
          </button>
        ))}
      </div>
    </div>
  );
}

```

## Standalone (no runtime)

Standalone, the element is fully controlled: you own the decision and what it grants.

```tsx
"use client";

import { useState } from "react";
import { PermissionGrant, type GrantScope } from "@/components/assistant-ui/elements/permission-grant";

export function ShellApproval() {
  const [scope, setScope] = useState<GrantScope | "pending">("pending");
  return (
    <PermissionGrant
      capability="Run shell commands"
      requester="deploy-agent"
      reach={["./workspace/**", "npm run *"]}
      scope={scope}
      onGrant={setScope}
    />
  );
}

```
