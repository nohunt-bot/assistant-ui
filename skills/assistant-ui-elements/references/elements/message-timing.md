# Message timing  (`message-timing`)

Streaming statistics for the current message, including first token, total time, and speed.

- 完整文件：`references/pages/message-timing.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/message-timing.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-message-timing.md`

## With a runtime

Every assistant-ui runtime records how a response streamed in. The badge reads that recording through `useMessageTiming()` and stays invisible until it has something to report.

```tsx
"use client";

import { useMessageTiming } from "@assistant-ui/react";
import {
  Tooltip,
  TooltipContent,
  TooltipProvider,
  TooltipTrigger,
} from "@/components/ui/tooltip";
import type { FC } from "react";

const formatTimingMs = (ms: number | undefined): string => {
  if (ms === undefined) return "—";
  if (ms < 1000) return `${Math.round(ms)}ms`;
  return `${(ms / 1000).toFixed(2)}s`;
};

export const MessageTiming: FC<{
  className?: string;
  side?: "top" | "right" | "bottom" | "left";
}> = ({ className, side = "right" }) => {
  const timing = useMessageTiming();
  if (timing?.totalStreamTime === undefined) return null;

  return (
    <TooltipProvider>
      <Tooltip>
        <TooltipTrigger
          render={
            <button
              type="button"
              aria-label="Message timing"
              className={className}
            />
          }
        >
          {formatTimingMs(timing.totalStreamTime)}
        </TooltipTrigger>
        <TooltipContent side={side} sideOffset={8}>
          {/* first token, total, speed, chunks; see Anatomy below */}
        </TooltipContent>
      </Tooltip>
    </TooltipProvider>
  );
};
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, there is nothing to time, because the badge reads a runtime's stream telemetry. Use the stats row from [The timing-footer design](#the-timing-footer-design), which takes the numbers as props.
