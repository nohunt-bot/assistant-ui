# Timeline  (`timeline`)

Events on a time axis, with what already happened and what is still coming.

- 完整文件：`references/pages/timeline.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-timeline.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-timeline.md`

## With a runtime

This element has no assistant-ui primitive of its own, so the runtime wiring is a tool renderer rather than a primitive composition. A timeline is usually a lookup against something you track elsewhere, so the events belong on the tool's `result` rather than on its streamed `args`.

```tsx
"use client";

import { defineToolkit } from "@assistant-ui/react";
import { Timeline } from "@/components/assistant-ui/elements/timeline";

export const toolkit = defineToolkit({
  get_incident_timeline: {
    type: "backend",
    render: ({ args, result }) => {
      if (!result) return <p>Loading the timeline for {args.issueId}…</p>;
      return (
        <Timeline
          events={result.events}
          visibleCount={result.events.length}
        />
      );
    },
  },
});

```

## Standalone (no runtime)

Standalone, the element is a plain display component: it owns no state of its own, so the minimal usage is a constant set of events shown all at once.

```tsx
"use client";

import {
  Timeline,
  type TimelineEvent,
} from "@/components/assistant-ui/elements/timeline";

const events: readonly TimelineEvent[] = [
  { id: "1", when: "past", time: "09:02", title: "Issue filed" },
  { id: "2", when: "past", time: "09:40", title: "Reproduced" },
  { id: "3", when: "now", time: "10:15", title: "Fix in review" },
  { id: "4", when: "future", time: "11:00", title: "Release 0.14.1" },
];

export function IssueTimeline() {
  return <Timeline events={events} visibleCount={events.length} />;
}

```
