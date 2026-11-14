# Schedule  (`schedule-card`)

A run that repeats on its own, with its cadence and how it has been doing.

- 完整文件：`references/pages/schedule-card.md`（API reference、Anatomy、更多範例）
- 形式：standalone
- 安裝（standalone）：`source/<flavor>/aui/elements-schedule-card.md`

## Standalone (no runtime)

import { ScheduleCard, type ScheduleRun } from "@/components/assistant-ui/elements/schedule-card";

```tsx
"use client";

import { ScheduleCard, type ScheduleRun } from "@/components/assistant-ui/elements/schedule-card";

const history: ScheduleRun[] = [
  { id: "1", at: "Today 06:00", ok: true },
  { id: "2", at: "Yesterday 06:00", ok: true },
  { id: "3", at: "Tue 06:00", ok: false },
];

export function NightlyReport() {
  return (
    <ScheduleCard
      name="Nightly report"
      cadence="Daily at 06:00"
      nextRun="Tomorrow 06:00"
      enabled
      history={history}
    />
  );
}

```
