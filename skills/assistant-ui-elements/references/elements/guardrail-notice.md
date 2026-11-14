# Guardrail notice  (`guardrail-notice`)

A refusal in its own shape, with the nearest thing it can do instead.

- 完整文件：`references/pages/guardrail-notice.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-guardrail-notice.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-guardrail-notice.md`

## With a runtime

A refusal is not a separate concept from a normal message: it is an assistant message whose status finished as `"incomplete"` with reason `"content-filter"`. Everything else on the notice, the title, the explanation, and the alternatives, is content your app supplies; the runtime only tells you that the stop happened.

```tsx
"use client";

import { useAuiState } from "@assistant-ui/react";

function useIsGuardrailStop() {
  return useAuiState((s) => {
    const status = s.message.status;
    return status?.type === "incomplete" && status.reason === "content-filter";
  });
}

```

## Standalone (no runtime)

Standalone, showing the notice at all, and what it says, is entirely your call.

```tsx
"use client";

import { GuardrailNotice } from "@/components/assistant-ui/elements/guardrail-notice";

export function RefusalNotice() {
  return (
    <GuardrailNotice
      title="I can't help with that"
      explanation="This asks for a working exploit against infrastructure you don't own."
      policy="policy"
      alternatives={["Explain how to defend against this instead"]}
    />
  );
}

```
