# label（`ui/label`，base）

- 直接依賴：無
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `ui/label`
- npm（整組）：`npm install cn`

## 檔案：`components/ui/label.tsx`

```tsx
"use client"

import * as React from "react"
import { cn } from "cn"

function Label({ className, ...props }: React.ComponentProps<"label">) {
  return (
    <label
      data-slot="label"
      className={cn(
        "flex items-center gap-2 text-sm leading-none font-medium select-none group-data-[disabled=true]:pointer-events-none group-data-[disabled=true]:opacity-50 peer-disabled:cursor-not-allowed peer-disabled:opacity-50",
        className
      )}
      {...props}
    />
  )
}

export { Label }
```
