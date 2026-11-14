# Directive text  (`directive-text`)

A message renderer that turns mention directives into inline, runtime-aware chips.

- 完整文件：`references/pages/directive-text.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/directive-text.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-directive-text.md`

## With a runtime

`DirectiveText` is pre-wired with assistant-ui's default directive format. Pass it as the `Text` renderer on `MessagePrimitive.Parts` wherever directive chips should appear.

```tsx
import { DirectiveText } from "@/components/assistant-ui/elements/directive-text.aui";
import { MessagePrimitive } from "@assistant-ui/react";

function UserMessage() {
  return (
    <MessagePrimitive.Root>
      <MessagePrimitive.Parts components={{ Text: DirectiveText }} />
    </MessagePrimitive.Root>
  );
}

```

## Standalone (no runtime)

Standalone, `createDirectiveText` is a factory: give it your own parser and it returns a component that renders the parsed segments, no runtime involved.

```tsx
import {
  createDirectiveText,
  type DirectiveTextFormatter,
  type DirectiveTextSegment,
} from "@/components/assistant-ui/elements/directive-text";

const bracketFormatter: DirectiveTextFormatter = {
  parse(text) {
    const segments: DirectiveTextSegment[] = [];
    const re = /@\[([^\]]+)\]\(([^)]+)\)/g;
    let last = 0;
    for (const m of text.matchAll(re)) {
      if (m.index > last) segments.push({ kind: "text" as const, text: text.slice(last, m.index) });
      segments.push({ kind: "mention" as const, type: "user", label: m[1]!, id: m[2]! });
      last = m.index + m[0].length;
    }
    if (last < text.length) segments.push({ kind: "text" as const, text: text.slice(last) });
    return segments;
  },
};

const MentionText = createDirectiveText(bracketFormatter);

export function Message({ text }: { text: string }) {
  return <MentionText text={text} />;
}

```
