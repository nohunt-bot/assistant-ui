# Tooltip Icon Button（`aui/tooltip-icon-button`，base）

Icon button with an accessible tooltip label, shared across the assistant UI components.

- 直接依賴：`ui/tooltip`, `ui/button`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `ui/tooltip`, `ui/button`, `aui/tooltip-icon-button`
- npm（整組）：`npm install cn`

## 檔案：`components/assistant-ui/elements/tooltip-icon-button.tsx`

```tsx
"use client";

import { type ComponentPropsWithRef, forwardRef } from "react";

import {
  Tooltip,
  TooltipContent,
  TooltipProvider,
  TooltipTrigger,
} from "@/components/ui/tooltip";
import { Button } from "@/components/ui/button";
import { cn } from "@/lib/utils";

export type TooltipIconButtonProps = ComponentPropsWithRef<typeof Button> & {
  tooltip: string;
  side?: "top" | "bottom" | "left" | "right";
};

export const TooltipIconButton = forwardRef<
  HTMLButtonElement,
  TooltipIconButtonProps
>(({ children, tooltip, side = "bottom", className, ...rest }, ref) => {
  return (
    <TooltipProvider>
      <Tooltip>
        <TooltipTrigger
          render={
            <Button
              variant="ghost"
              size="icon"
              {...rest}
              className={cn(
                "aui-button-icon size-6 p-1 active:scale-90",
                className,
              )}
              ref={ref}
            />
          }
        >
          {children}
          <span className="aui-sr-only sr-only">{tooltip}</span>
        </TooltipTrigger>
        <TooltipContent side={side}>{tooltip}</TooltipContent>
      </Tooltip>
    </TooltipProvider>
  );
});

TooltipIconButton.displayName = "TooltipIconButton";
```
