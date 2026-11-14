# Todo list  (`todo-list`)

The agent's own working list, rewritten mid-run as it discovers what else is needed.

- 完整文件：`references/pages/todo-list.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-todo-list.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-todo-list.md`

## With a runtime

Nothing in the runtime tracks a todo list on its own. The list is ordinary tool output: define a tool whose arguments are the current items, and render it with the `TodoList` element.

```tsx
import { defineToolkit } from "@assistant-ui/react";
import { z } from "zod";
import { TodoList } from "@/components/assistant-ui/elements/todo-list";

export const toolkit = defineToolkit({
  update_todos: {
    type: "frontend",
    description: "Replace the visible working list with the current plan.",
    parameters: z.object({
      title: z.string().optional(),
      description: z.string().optional(),
      maxVisible: z.number().optional(),
      items: z.array(
        z.object({
          id: z.string(),
          text: z.string(),
          description: z.string().optional(),
          status: z.enum(["pending", "active", "done", "failed", "cancelled"]),
          reason: z.string().optional(),
        }),
      ),
    }),
    execute: async ({ items }) => ({ count: items.length }),
    render: ({ args }) => (
      <TodoList
        title={args.title}
        description={args.description}
        maxVisible={args.maxVisible}
        items={args.items ?? []}
      />
    ),
  },
});

```

## Standalone (no runtime)

Standalone, the element is fully controlled: you hold the array of items and hand it the whole list on every render.

```tsx
"use client";

import { useState } from "react";
import { TodoList, type TodoItem } from "@/components/assistant-ui/elements/todo-list";

const initialItems: TodoItem[] = [
  { id: "1", text: "Read the failing test", description: "Trace the current behavior.", status: "done" },
  { id: "2", text: "Reproduce locally", status: "active" },
  { id: "3", text: "Write the fix", status: "pending" },
  { id: "4", text: "Re-run the suite", status: "failed", reason: "Tests timed out" },
  { id: "5", text: "Update the old fixture", status: "cancelled" },
];

export function Plan() {
  const [items, setItems] = useState(initialItems);
  return <TodoList items={items} />;
}

```
