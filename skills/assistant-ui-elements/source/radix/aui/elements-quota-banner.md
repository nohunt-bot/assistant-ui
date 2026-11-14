# Quota（`aui/elements-quota-banner`，radix）

How much is left, when it comes back, and the way to get more.

- 直接依賴：`aui/elements-surfaces`, `aui/elements-range`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/elements-surfaces`, `aui/elements-range`, `aui/elements-quota-banner`
- npm（整組）：`npm install cn tw-shimmer@^0.4.14`

## 檔案：`components/assistant-ui/elements/quota-banner.tsx`

```tsx
"use client";

import type { ComponentProps } from "react";
import { cn } from "@/lib/utils";
import { inkButton, mono, paper } from "./surfaces";
import { announced, pct } from "../utils/range";

export function QuotaBanner({
  used,
  limit,
  unit,
  resetsIn,
  upgradeLabel,
  onUpgrade,
  className,
  ...props
}: Omit<
  ComponentProps<"div">,
  | "children"
  | "used"
  | "limit"
  | "unit"
  | "resetsIn"
  | "upgradeLabel"
  | "onUpgrade"
> & {
  used: number;
  limit: number;
  unit: string;
  resetsIn: string;
  upgradeLabel: string;
  onUpgrade?: () => void;
}) {
  const left = Math.max(0, limit - used);
  const ratio = limit === 0 ? 0 : used / limit;
  const exhausted = limit > 0 && left === 0;
  const tight = !exhausted && ratio >= 0.9;

  return (
    <div
      data-slot="quota-banner"
      className={cn(
        paper,
        "flex w-full max-w-sm flex-col gap-2.5 rounded-2xl p-3.5",
        className,
      )}

      {...props}
    >
      <div className="flex items-baseline gap-2">
        <span
          className={cn(
            "text-[13.5px] font-medium",
            exhausted && "text-red-600 dark:text-red-400",
            tight && "text-amber-700 dark:text-amber-400",
          )}
        >
          {left} {unit} left
        </span>
        <span
          className={cn(mono, "text-muted-foreground ms-auto tabular-nums")}
        >
          resets in {resetsIn}
        </span>
      </div>

      <span
        role="meter"
        aria-label={`${unit} used`}
        aria-valuemin={0}
        aria-valuemax={100}
        aria-valuenow={announced(pct(used, limit))}
        aria-valuetext={`${used} of ${limit} ${unit} used`}
        className="bg-foreground/[0.06] inset-ring-border h-1 w-full overflow-hidden rounded-full inset-ring forced-colors:border"
      >
        <span
          className={cn(
            "block h-full rounded-full transition-[width] duration-500 forced-color-adjust-none motion-reduce:transition-none",
            exhausted
              ? "bg-red-500"
              : tight
                ? "bg-amber-500"
                : "bg-foreground/40",
          )}
          style={{ width: `${pct(used, limit)}%` }}
        />
      </span>

      <div className="flex items-center gap-2">
        <span className={cn(mono, "text-muted-foreground tabular-nums")}>
          {used} of {limit} used
        </span>
        <button
          type="button"
          onClick={onUpgrade}
          className={cn(
            inkButton,
            "ms-auto flex h-7 items-center rounded-full px-3 text-xs font-medium",
          )}
        >
          {upgradeLabel}
        </button>
      </div>
    </div>
  );
}
```
