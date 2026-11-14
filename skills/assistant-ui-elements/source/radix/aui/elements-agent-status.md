# Agent status（`aui/elements-agent-status`，radix）

One pill that always answers: what is it doing, and for how long.

- 直接依賴：`aui/elements-surfaces`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/elements-surfaces`, `aui/elements-agent-status`
- npm（整組）：`npm install cn tw-shimmer@^0.4.14 lucide-react`

## 檔案：`components/assistant-ui/elements/agent-status.tsx`

```tsx
"use client";

import type { ComponentProps, ReactNode } from "react";
import { CheckIcon, PauseIcon, RotateCcwIcon, XIcon } from "lucide-react";
import { cn } from "@/lib/utils";
import { mono, paper } from "./surfaces";

export type AgentState = "working" | "waiting" | "done" | "failed";

export interface StatusStep {
  state: AgentState;
  label: string;
}

export function AgentStatus({
  state,
  label,
  elapsed,
  trailing,
  className,
  ...props
}: Omit<ComponentProps<"span">, "children" | "state" | "label" | "elapsed"> & {
  state: AgentState;
  label: string;
  elapsed?: string | undefined;
  trailing?: ReactNode | undefined;
}) {
  return (
    <span
      data-slot="agent-status"
      className={cn(
        paper,
        "flex items-center gap-2.5 rounded-full py-1.5 ps-3.5 pe-1.5",
        className,
      )}
      {...props}
    >
      {state === "done" ? (
        <CheckIcon aria-hidden className="size-3 shrink-0 text-emerald-500" />
      ) : state === "failed" ? (
        <XIcon aria-hidden className="text-destructive size-3 shrink-0" />
      ) : (
        <span
          aria-hidden
          className={cn(
            "size-1.5 shrink-0 rounded-full motion-reduce:animate-none",
            state === "working"
              ? "animate-pulse bg-blue-500 dark:bg-blue-400"
              : "border-foreground/35 border",
          )}
        />
      )}
      <span className="sr-only">{state}</span>
      <span
        key={label}
        className="fade-in blur-in-[2px] animate-in max-w-44 truncate text-xs duration-300 motion-reduce:animate-none"
      >
        {label}
      </span>
      {elapsed !== undefined && state !== "done" && state !== "failed" && (
        <span className={cn(mono, "text-muted-foreground tabular-nums")}>
          {elapsed}
        </span>
      )}
      <span
        aria-hidden
        data-slot="agent-status-trailing"
        className="text-muted-foreground flex size-6 items-center justify-center rounded-full"
      >
        {trailing !== undefined ? (
          trailing
        ) : state === "done" || state === "failed" ? (
          <RotateCcwIcon className="size-3" />
        ) : (
          <PauseIcon className="size-3" />
        )}
      </span>
    </span>
  );
}
```
