# Directive Text（`aui/directive-text`，base）

Render mention directives as inline chips in user messages.

- 直接依賴：`aui/elements-directive-text`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/badge`, `aui/elements-directive-text`, `aui/directive-text`
- npm（整組）：`npm install cn class-variance-authority @base-ui/react @assistant-ui/react@^0.15.36`

## 檔案：`components/assistant-ui/elements/directive-text.aui.tsx`

```tsx
"use client";

import { memo } from "react";
import type { TextMessagePartComponent } from "@assistant-ui/react";
import type { DirectiveFormatter } from "@assistant-ui/react";
import { defaultDirectiveFormatter } from "@assistant-ui/react";
import {
  createDirectiveText as createDirectiveTextBase,
  type CreateDirectiveTextOptions,
} from "./directive-text";

export type {
  CreateDirectiveTextOptions,
  DirectiveTextFormatter,
  DirectiveTextSegment,
} from "./directive-text";

/** Creates a `Text` message part component that parses directive syntax and renders inline chips. */
export function createDirectiveText(
  formatter: DirectiveFormatter,
  options?: CreateDirectiveTextOptions,
): TextMessagePartComponent {
  return createDirectiveTextBase(formatter, options);
}

/** `Text` message part component that renders directive syntax as inline chips. */
export const DirectiveText: TextMessagePartComponent = memo(
  createDirectiveTextBase(defaultDirectiveFormatter),
);
```
