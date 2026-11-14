# Typing indicator（`aui/elements-typing-indicator`，radix）

The classic three dots, tuned to read as presence rather than noise.

- 直接依賴：`aui/elements-surfaces`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/elements-surfaces`, `aui/elements-typing-indicator`
- npm（整組）：`npm install cn tw-shimmer@^0.4.14`

## 檔案：`components/assistant-ui/elements/typing-indicator.tsx`

```tsx
"use client";

import type { ComponentProps } from "react";
import { cn } from "@/lib/utils";
import { paper } from "./surfaces";

const DOT_DELAYS = ["-0.32s", "-0.16s", "0s"];

export function TypingIndicator({
  variant = "bubble",
  className,
  ...props
}: Omit<
  ComponentProps<"div">,
  "children" | "variant" | "role" | "aria-label"
> & {
  variant?: "bubble" | "bare";
}) {
  const dots = DOT_DELAYS.map((delay) => (
    <span
      key={delay}
      aria-hidden
      className="bg-foreground/40 size-1.5 animate-bounce rounded-full motion-reduce:animate-none"
      style={{ animationDelay: delay, animationDuration: "1.1s" }}
    />
  ));

  if (variant === "bare") {
    return (
      <div
        data-slot="typing-indicator"
        data-variant="bare"
        role="status"
        aria-label="Assistant is typing"
        className={cn("flex gap-1", className)}
        {...props}
      >
        {dots}
      </div>
    );
  }

  return (
    <div
      data-slot="typing-indicator"
      data-variant="bubble"
      className={cn(paper, "w-fit rounded-full px-4 py-3.5", className)}
      {...props}
    >
      <div
        role="status"
        aria-label="Assistant is typing"
        className="flex gap-1"
      >
        {dots}
      </div>
    </div>
  );
}
```
