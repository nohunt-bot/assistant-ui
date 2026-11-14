# Elicitation form  (`elicitation-form`)

A server pausing mid-tool-call to ask you for the fields it still needs.

- 完整文件：`references/pages/elicitation-form.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-elicitation-form.md`

## With a runtime

MCP form elicitation is unstyled primitives, not a toolkit renderer: `@assistant-ui/react-mcp` exposes `McpElicitationPrimitive`, and you build the same look this element has directly from its parts.

```tsx
"use client";

import { PlugIcon } from "lucide-react";
import { McpElicitationPrimitive } from "@assistant-ui/react-mcp";
import { cn } from "@/lib/utils";
import { field, inkButton, mono, paper } from "@/components/assistant-ui/elements/surfaces";

function fieldKind(schema: unknown): "text" | "toggle" {
  return (schema as { type?: string } | undefined)?.type === "boolean"
    ? "toggle"
    : "text";
}

export function McpElicitationForm({ serverName }: { serverName: string }) {
  return (
    <McpElicitationPrimitive.Items>
      {() => (
        <McpElicitationPrimitive.Root
          className={cn(paper, "flex w-full max-w-sm flex-col gap-3.5 rounded-[20px] p-4")}
        >
          <div className="flex items-center gap-2.5">
            <span className="bg-foreground/[0.05] text-muted-foreground flex size-7 items-center justify-center rounded-lg">
              <PlugIcon className="size-3.5" />
            </span>
            <span className="min-w-0 flex-1 truncate text-[13.5px] font-medium">
              {serverName}
            </span>
          </div>

          <McpElicitationPrimitive.Message className="text-muted-foreground text-xs leading-relaxed" />
          <McpElicitationPrimitive.Error className="text-xs text-red-600 dark:text-red-400" />

          <div className="flex flex-col gap-2.5">
            <McpElicitationPrimitive.Fields>
              {({ name, schema, value, setValue }) => (
                <div className="flex flex-col gap-1">
                  <span className={cn(mono, "text-muted-foreground")}>{name}</span>
                  {fieldKind(schema) === "toggle" ? (
                    <input
                      type="checkbox"
                      checked={value === true}
                      onChange={(e) => setValue(e.target.checked)}
                    />
                  ) : (
                    <input
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, the element is fully controlled: you own the field list, the values inside it, and which of the two settled states it shows.

```tsx
"use client";

import { useState } from "react";
import {
  ElicitationForm,
  type ElicitationField,
  type ElicitationState,
} from "@/components/assistant-ui/elements/elicitation-form";

const INITIAL_FIELDS: readonly ElicitationField[] = [
  { name: "repo", label: "Repository", value: "assistant-ui/assistant-ui", kind: "text", required: true },
  { name: "notify", label: "Notify watchers", value: "true", kind: "toggle" },
];

export function PublishRequest() {
  const [state, setState] = useState<ElicitationState>("request");
  const [fields, setFields] = useState(INITIAL_FIELDS);

  return (
    <ElicitationForm
      server="github-mcp"
      message="Confirm where the release notes should be published."
      fields={fields}
      state={state}
      onFieldChange={(name, value) =>
        setFields((current) =>
          current.map((item) => (item.name === name ? { ...item, value } : item)),
        )
      }
      onAccept={() => setState("accepted")}
      onDecline={() => setState("declined")}
    />
  );
}

```
