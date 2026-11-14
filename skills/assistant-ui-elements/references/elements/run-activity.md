# Run activity  (`run-activity`)

A compact live summary, ordered commentary and tools, and a final answer that stays visible.

- 完整文件：`references/pages/run-activity.md`（API reference、Anatomy、更多範例）
- 形式：runtime
- 安裝（runtime）：`source/<flavor>/aui/elements-run-activity.md`

## With a runtime

function CompletedRun() {
  const [open, setOpen] = useState(false);
  return (
    <RunActivity
      status="complete"
      statusLabel="Worked for"
      durationLabel="2m 13s"
      open={open}
      onOpenChange={setOpen}
      entries={[
        {
          id: "commentary-1",
          kind: "commentary",
          label: "Inspecting the files",
          content: <p>I’ll inspect the files.</p>,
        },
        {
          id: "read-1",
          kind: "tool",
          label: "Reading thread.tsx",
          content: <p>Read thread.tsx</p>,
        },
        {
          id: "commentary-2",
          kind: "commentary",
          label: "Checking the fix",
          content: <p>I found the issue; checking the fix.</p>,
        },
        {
          id: "test-1",
          kind: "tool",
          label: "Running the tests",
          content: <p>All tests passed.</p>,
        },
      ]}
    >
      <p>The fix is ready. The tests pass.</p>
    </RunActivity>
  );
}
```

```tsx
import { useState } from "react";
import { RunActivity } from "@/components/assistant-ui/elements/run-activity";

function CompletedRun() {
  const [open, setOpen] = useState(false);
  return (
    <RunActivity
      status="complete"
      statusLabel="Worked for"
      durationLabel="2m 13s"
      open={open}
      onOpenChange={setOpen}
      entries={[
        {
          id: "commentary-1",
          kind: "commentary",
          label: "Inspecting the files",
          content: <p>I’ll inspect the files.</p>,
        },
        {
          id: "read-1",
          kind: "tool",
          label: "Reading thread.tsx",
          content: <p>Read thread.tsx</p>,
        },
        {
          id: "commentary-2",
          kind: "commentary",
          label: "Checking the fix",
          content: <p>I found the issue; checking the fix.</p>,
        },
        {
          id: "test-1",
          kind: "tool",
          label: "Running the tests",
          content: <p>All tests passed.</p>,
        },
      ]}
    >
      <p>The fix is ready. The tests pass.</p>
    </RunActivity>
  );
}
```
