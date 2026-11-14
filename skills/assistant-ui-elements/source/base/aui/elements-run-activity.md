# Run activity（`aui/elements-run-activity`，base）

Ordered commentary and tools, with a live summary and a final answer that stays visible.

- 直接依賴：`aui/elements-surfaces`, `aui/elements-collapsible-root`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/elements-surfaces`, `aui/elements-collapsible-root`, `aui/elements-run-activity`
- npm（整組）：`npm install cn tw-shimmer@^0.4.14 @base-ui/react lucide-react`

## 檔案：`components/assistant-ui/elements/run-activity.tsx`

```tsx
"use client";

import type { ReactNode } from "react";
import { ChevronRightIcon } from "lucide-react";
import { cn } from "@/lib/utils";
import {
  CollapsibleRoot as Collapsible,
  CollapsibleContent,
  CollapsibleTrigger,
} from "./collapsible-root";
import { collapsePanel } from "./surfaces";

export type RunActivityStatus =
  | "running"
  | "requires-action"
  | "complete"
  | "cancelled"
  | "incomplete"
  | "error";

export type RunActivityEntry = {
  id: string;
  kind: "commentary" | "tool";
  label: string;
  content: ReactNode;
};

export type RunActivityProps = {
  status: RunActivityStatus;
  statusLabel: string;
  /** Formatted from the caller's persisted run timing. */
  durationLabel?: ReactNode;
  entries: readonly RunActivityEntry[];
  open: boolean;
  onOpenChange: (open: boolean) => void;
  /** Pending decisions and recovery controls remain outside the disclosure. */
  attention?: ReactNode;
  /** Final-answer content remains outside the disclosure. */
  children?: ReactNode;
  className?: string;
};

export function RunActivity({
  status,
  statusLabel,
  durationLabel,
  entries,
  open,
  onOpenChange,
  attention,
  children,
  className,
}: RunActivityProps) {
  const latestActivity =
    status === "running"
      ? entries.findLast((entry) => entry.label.trim())?.label
      : undefined;

  const summary = (
    <span className="text-start tabular-nums">
      {statusLabel}
      {durationLabel != null && <> {durationLabel}</>}
    </span>
  );

  return (
    <div
      data-slot="run-activity"
      data-status={status}
      className={cn("flex min-w-0 flex-col gap-3", className)}
    >
      {entries.length > 0 ? (
        <Collapsible open={open} onOpenChange={onOpenChange}>
          <CollapsibleTrigger
            aria-busy={status === "running"}
            className="group/trigger text-muted-foreground hover:text-foreground focus-visible:ring-ring/50 flex max-w-full items-center gap-1.5 rounded-md py-1 text-sm outline-none focus-visible:ring-[3px]"
          >
            <ChevronRightIcon
              aria-hidden="true"
              className="size-3.5 shrink-0 transition-transform duration-200 group-data-open/trigger:rotate-90 group-data-panel-open/trigger:rotate-90 motion-reduce:transition-none"
            />
            {summary}
          </CollapsibleTrigger>
          <CollapsibleContent className={cn(collapsePanel, "peer")}>
            <ol className="border-border ms-1.5 flex min-w-0 flex-col gap-3 border-s ps-3.5 pt-2 text-sm wrap-anywhere">
              {entries.map((entry) => (
                <li key={entry.id} data-activity-kind={entry.kind}>
                  {entry.content}
                </li>
              ))}
            </ol>
          </CollapsibleContent>
          {!open && latestActivity && (
            // The panel stays mounted during its exit transition.
            <p className="text-muted-foreground ps-5 text-sm wrap-anywhere peer-[:not([hidden])]:hidden">
              {latestActivity}
            </p>
          )}
        </Collapsible>
      ) : (
        <p className="text-muted-foreground py-1 text-sm">{summary}</p>
      )}
      {/* Announce the settled duration without announcing streaming timer ticks. */}
      <span role="status" aria-atomic="true" className="sr-only">
        {statusLabel}
        {status !== "running" && durationLabel != null && <> {durationLabel}</>}
      </span>
      {attention}
      {children}
    </div>
  );
}
```
