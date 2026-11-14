# separator（`ui/separator`，base）

- 直接依賴：無
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `ui/separator`
- npm（整組）：`npm install cn`

## 檔案：`components/ui/separator.tsx`

```tsx
"use client"

import { Separator as SeparatorPrimitive } from "@base-ui/react/separator"
import { cn } from "cn"

function Separator({
  className,
  orientation = "horizontal",
  ...props
}: SeparatorPrimitive.Props) {
  return (
    <SeparatorPrimitive
      data-slot="separator"
      orientation={orientation}
      className={cn(
        "shrink-0 bg-border data-horizontal:h-px data-horizontal:w-full data-vertical:w-px data-vertical:self-stretch",
        className
      )}
      {...props}
    />
  )
}

export { Separator }
```
