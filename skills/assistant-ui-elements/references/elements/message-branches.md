# Message branches  (`message-branches`)

Navigate between regenerated versions of the same answer without losing your place.

- 完整文件：`references/pages/message-branches.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-message-branches.md`
- Thread 內建：The `Thread` element already ships this composition for both user and assistant messages, so installing `@assistant-ui/thread` gives you branch navigation without any of the above.

## With a runtime

Every assistant-ui runtime tracks branches per message. The stepper reads `branchNumber` and `branchCount` from the message and switches branches through the runtime, so nothing is stored in component state.

```tsx
"use client";

import { BranchPickerPrimitive } from "@assistant-ui/react";
import { ChevronLeftIcon, ChevronRightIcon } from "lucide-react";
import { cn } from "@/lib/utils";
import { ghostButton, mono } from "@/components/assistant-ui/elements/surfaces";

export function BranchPicker() {
  return (
    <BranchPickerPrimitive.Root
      hideWhenSingleBranch
      className="flex items-center gap-1"
    >
      <BranchPickerPrimitive.Previous
        aria-label="Show previous response"
        className={cn(ghostButton, "size-6")}
      >
        <ChevronLeftIcon className="size-3.5" />
      </BranchPickerPrimitive.Previous>
      <span className={cn(mono, "text-muted-foreground tabular-nums")}>
        <BranchPickerPrimitive.Number /> / <BranchPickerPrimitive.Count />
      </span>
      <BranchPickerPrimitive.Next
        aria-label="Show next response"
        className={cn(ghostButton, "size-6")}
      >
        <ChevronRightIcon className="size-3.5" />
      </BranchPickerPrimitive.Next>
    </BranchPickerPrimitive.Root>
  );
}

```

## Standalone (no runtime)

Standalone, the element is a controlled component: you own the list of variants and the active index. It renders the active variant and the stepper, and reports index changes back to you.

```tsx
"use client";

import { useState } from "react";
import { MessageBranches } from "@/components/assistant-ui/elements/message-branches";

const variants = [
  "Paris is the capital of France.",
  "The capital of France is Paris, on the Seine.",
  "France's capital city is Paris.",
];

export function Answer() {
  const [index, setIndex] = useState(0);
  return (
    <MessageBranches variants={variants} index={index} onIndexChange={setIndex} />
  );
}

```
