# Follow-up suggestions  (`follow-up-suggestions`)

Prompt chips populated from the runtime's generated follow-up suggestions.

- 完整文件：`references/pages/follow-up-suggestions.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/follow-up-suggestions.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- Thread 內建：`Thread` already renders this in its viewport footer; use it directly only if you're composing your own layout.

## With a runtime

`Thread` already renders this in its viewport footer; use it directly only if you're composing your own layout.

```tsx
const runtime = useExternalStoreRuntime({
  messages,
  convertMessage,
  onNew: async (message) => {
    /* append the message in your store */
  },
  suggestions: [
    { prompt: "Summarize this as action items" },
    { prompt: "Write a shorter version" },
  ],
});

```

## Standalone (no runtime)

Standalone, there is no runtime to generate a list from. Render your own chip row with the static `Suggestions` component instead (see [The paper-pill design](#the-paper-pill-design)).
