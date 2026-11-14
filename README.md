# assistant-ui

給 Claude Code 用的 skill：在不能連外網的環境選用、安裝 [assistant-ui](https://www.assistant-ui.com) 的 element。全部都是 md 檔。

## skills/assistant-ui-elements

- `SKILL.md`：選用流程、離線安裝步驟，以及 128 個 element 的決策表（說明什麼情境用哪一個）
- `references/elements/<slug>.md`：每個 element 的用途、要讀的原始碼檔、最小範例
- `references/pages/<slug>.md`：官方完整文件
- `references/docs/`：React 相關文件（runtimes、tools、guides 等）
- `source/<base|radix>/{aui,ui}/<name>.md`：元件原始碼，有 Base UI 和 Radix 兩套，每段 code block 就是一個檔案的內容

內容抓取日期：2026-11-14。

## 安裝

把 `skills/assistant-ui-elements` 放進 `~/.claude/skills/`（個人）或專案的 `.claude/skills/`：

```bash
git clone https://github.com/nohunt-bot/assistant-ui.git
cp -R assistant-ui/skills/assistant-ui-elements ~/.claude/skills/
```

內網機器不能 clone 時，在外網機器 clone 後把 `skills/assistant-ui-elements` 資料夾整個帶進去。

元件需要的 npm 套件（例如 `@assistant-ui/react`、`lucide-react`）要從公司的 npm 鏡像安裝。
