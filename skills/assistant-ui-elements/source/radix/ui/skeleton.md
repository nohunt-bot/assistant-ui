# skeleton（`ui/skeleton`，radix）

- 直接依賴：無
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `ui/skeleton`
- npm（整組）：`npm install cn`

## 檔案：`components/ui/skeleton.tsx`

```tsx
import { cn } from "cn"

function Skeleton({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="skeleton"
      className={cn("animate-pulse rounded-md bg-accent", className)}
      {...props}
    />
  )
}

export { Skeleton }
```
