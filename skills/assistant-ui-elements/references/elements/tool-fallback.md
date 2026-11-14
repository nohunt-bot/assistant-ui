# Tool fallback  (`tool-fallback`)

The default runtime renderer for tool calls that do not have dedicated UI.

- 完整文件：`references/pages/tool-fallback.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/tool-fallback.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- Thread 內建：`Thread` already renders this for every tool call that doesn't have its own UI.

## With a runtime

`Thread` already renders this for every tool call that doesn't have its own UI.

```tsx
case "tool-call":
  return part.toolUI ?? <ToolFallbackComponent {...part} />;

```

## Standalone (no runtime)

Standalone, there's nothing to fall back to: `ToolFallback` exists specifically to render whatever `ToolCallMessagePart` a runtime handed it and to call back into that same run, so a props-only version would be a static picture of the same shape you already have. Build your own tool-call card directly against your own data instead of reaching for this element.
