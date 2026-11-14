# Quota banner  (`quota-banner`)

How much is left, when it comes back, and the way to get more.

- 完整文件：`references/pages/quota-banner.md`（API reference、Anatomy、更多範例）
- 形式：runtime
- 安裝（runtime）：`source/<flavor>/aui/elements-quota-banner.md`

## With a runtime

You decide what "used" and "limit" mean, in whatever unit your plan counts, messages, tokens, or requests, and pass the current numbers down from wherever your account state lives.

```tsx
"use client";

import { useState } from "react";
import { QuotaBanner } from "@/components/assistant-ui/elements/quota-banner";

export function Quota() {
  const [used, setUsed] = useState(32);

  return (
    <QuotaBanner
      used={used}
      limit={50}
      unit="messages"
      resetsIn="3h 12m"
      upgradeLabel="Upgrade"
      onUpgrade={() => (window.location.href = "/billing")}
    />
  );
}

```
