# Collapsible Root（`aui/elements-collapsible-root`，base）

Ref-bearing disclosure root for assistant-ui components.

- 直接依賴：無
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/elements-collapsible-root`
- npm（整組）：`npm install cn @base-ui/react`

## 檔案：`components/assistant-ui/elements/collapsible-root.tsx`

```tsx
"use client";

import { Collapsible } from "@base-ui/react/collapsible";
import { forwardRef } from "react";

export const CollapsibleRoot = forwardRef<
  React.ComponentRef<typeof Collapsible.Root>,
  React.ComponentPropsWithoutRef<typeof Collapsible.Root>
>(function CollapsibleRoot(props, ref) {
  return <Collapsible.Root ref={ref} data-slot="collapsible" {...props} />;
});

export function CollapsibleTrigger({ ...props }: Collapsible.Trigger.Props) {
  return <Collapsible.Trigger data-slot="collapsible-trigger" {...props} />;
}

export function CollapsibleContent({ ...props }: Collapsible.Panel.Props) {
  return <Collapsible.Panel data-slot="collapsible-content" {...props} />;
}
```
