# Regenerate with（`aui/elements-regenerate-menu`，radix）

Fork the same turn to a different model instead of rolling the same dice.

- 直接依賴：`aui/elements-surfaces`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/elements-surfaces`, `aui/elements-regenerate-menu`
- npm（整組）：`npm install cn tw-shimmer@^0.4.14 lucide-react`

## 檔案：`components/assistant-ui/elements/regenerate-menu.tsx`

```tsx
"use client";

import type { ComponentProps } from "react";
import { RefreshCwIcon } from "lucide-react";
import { cn } from "@/lib/utils";
import { floating, ghostButton, mono } from "./surfaces";

export interface RegenerateOption {
  id: string;
  label: string;
  detail: string;
}

export function RegenerateMenu({
  options,
  open,
  currentId,
  onOpenChange,
  onPick,
  className,
  ...props
}: Omit<
  ComponentProps<"div">,
  "children" | "options" | "open" | "currentId" | "onOpenChange" | "onPick"
> & {
  options: readonly RegenerateOption[];
  open: boolean;
  currentId: string;
  onOpenChange?: (open: boolean) => void;
  onPick?: (id: string) => void;
}) {
  return (
    <div
      data-slot="regenerate-menu"
      className={cn("flex w-full max-w-sm flex-col gap-2", className)}

      {...props}
    >
      {onOpenChange && (
        <button
          type="button"
          aria-expanded={open}
          aria-label="Regenerate with a different model"
          onClick={() => onOpenChange(!open)}
          className={cn(
            ghostButton,
            "size-7 self-start",
            open && "bg-foreground/[0.06] text-foreground/90",
          )}
        >
          <RefreshCwIcon className="size-3.5" />
        </button>
      )}

      {open && (
        <div
          className={cn(
            floating,
            "fade-in zoom-in-95 slide-in-from-top-1 animate-in flex flex-col gap-0.5 rounded-2xl p-1.5 duration-200",
          )}
        >
          {options.map((option) => {
            const content = (
              <>
                <span className="min-w-0 flex-1 truncate text-[13px]">
                  {option.label}
                </span>
                <span className={cn(mono, "text-muted-foreground shrink-0")}>
                  {option.id === currentId ? "current" : option.detail}
                </span>
              </>
            );
            const className = onPick
              ? "hover:bg-foreground/[0.05] flex items-baseline gap-2 rounded-xl px-2.5 py-1.5 text-start transition-colors"
              : "flex items-baseline gap-2 rounded-xl px-2.5 py-1.5 text-start";

            return onPick ? (
              <button
                key={option.id}
                type="button"
                onClick={() => onPick(option.id)}
                className={className}
              >
                {content}
              </button>
            ) : (
              <div key={option.id} className={className}>
                {content}
              </div>
            );
          })}
        </div>
      )}
    </div>
  );
}
```
