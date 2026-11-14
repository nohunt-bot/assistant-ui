# Receipt focus（`aui/elements-receipt-focus`，base）

Keeps focus on a receipt when the focused answer is replaced.

- 直接依賴：無
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/elements-receipt-focus`
- npm（整組）：`npm install cn`

## 檔案：`components/assistant-ui/elements/receipt-focus.ts`

```ts
"use client";

import { useRef, type ComponentProps } from "react";
import { useIsomorphicLayoutEffect } from "../utils/useIsomorphicLayoutEffect";

type FocusHandlers = Pick<
  ComponentProps<"div">,
  "onFocusCapture" | "onBlurCapture"
>;

export function useReceiptFocus<T extends HTMLElement = HTMLElement>(
  handlers: FocusHandlers = {},
) {
  const focusedElementRef = useRef<HTMLElement | null>(null);
  const receiptRef = useRef<T | null>(null);

  useIsomorphicLayoutEffect(() => {
    const focusedElement = focusedElementRef.current;
    if (focusedElement && !focusedElement.isConnected) {
      focusedElementRef.current = null;
      if (
        focusedElement.ownerDocument.activeElement ===
        focusedElement.ownerDocument.body
      ) {
        receiptRef.current?.focus({ preventScroll: true });
      }
    }
  });

  return {
    receiptRef,
    focusHandlers: {
      onFocusCapture: (event) => {
        handlers.onFocusCapture?.(event);
        focusedElementRef.current = event.target as HTMLElement;
      },
      onBlurCapture: (event) => {
        handlers.onBlurCapture?.(event);
        if (
          event.target.isConnected &&
          !event.currentTarget.contains(
            event.relatedTarget ??
              event.currentTarget.ownerDocument.activeElement,
          )
        ) {
          focusedElementRef.current = null;
        }
      },
    } satisfies FocusHandlers,
  };
}
```

## 檔案：`components/assistant-ui/utils/useIsomorphicLayoutEffect.ts`

```ts
import { useEffect, useLayoutEffect } from "react";

const hasWindow =
  typeof (globalThis as typeof globalThis & { window?: unknown }).window !==
  "undefined";

export const useIsomorphicLayoutEffect = hasWindow
  ? useLayoutEffect
  : useEffect;
```
