# Handoff  (`agent-handoff`)

Control passing between agents, with the reason and what came along.

- 完整文件：`references/pages/agent-handoff.md`（API reference、Anatomy、更多範例）
- 形式：standalone
- 安裝（standalone）：`source/<flavor>/aui/elements-agent-handoff.md`

## Standalone (no runtime)

import { AgentHandoff } from "@/components/assistant-ui/elements/agent-handoff";

```tsx
"use client";

import { AgentHandoff } from "@/components/assistant-ui/elements/agent-handoff";

export function RoutingHandoff() {
  return (
    <AgentHandoff
      from="Router"
      to="Billing"
      reason="Question is about a refund, not routing."
      carried={["order #48213", "customer tier: pro"]}
      settled={false}
    />
  );
}

```
