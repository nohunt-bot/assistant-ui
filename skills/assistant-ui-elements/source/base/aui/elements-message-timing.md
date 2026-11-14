# Timing footer（`aui/elements-message-timing`，base）

What the turn cost: time to first token, throughput, tokens, money.

- 直接依賴：`aui/elements-surfaces`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/elements-surfaces`, `aui/elements-message-timing`
- npm（整組）：`npm install cn tw-shimmer@^0.4.14`

## 檔案：`components/assistant-ui/elements/message-timing.tsx`

```tsx
"use client";

import type { ComponentProps } from "react";
import { cn } from "@/lib/utils";
import { mono } from "./surfaces";

export interface TimingStat {
  label: string;
  value: string;
}

export function MessageTiming({
  stats,
  streaming,
  className,
  ...props
}: Omit<ComponentProps<"div">, "children" | "stats" | "streaming"> & {
  stats: readonly TimingStat[];
  streaming?: boolean;
}) {
  return (
    <div
      data-slot="message-timing"
      className={cn(
        "fade-in animate-in flex w-full max-w-sm flex-wrap items-center gap-x-3 gap-y-1 duration-500",
        className,
      )}

      {...props}
    >
      {stats.map((stat) => (
        <span key={stat.label} className="flex items-baseline gap-1">
          <span className={cn(mono, "text-muted-foreground")}>
            {stat.label}
          </span>
          <span
            className={cn(
              mono,
              "tabular-nums",
              streaming
                ? "text-blue-500 dark:text-blue-400"
                : "text-muted-foreground",
            )}
          >
            {stat.value}
          </span>
        </span>
      ))}
    </div>
  );
}
```
