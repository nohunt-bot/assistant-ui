---
name: assistant-ui-elements
description: "選用 assistant-ui 的 element（React 聊天 UI 元件，shadcn registry 安裝，共 128 個）：依情境決定該用 Thread、Composer、Thread list、modal/sidebar、tool UI、approval、reasoning、引用來源、語音、圖表等哪一個，附元件原始碼與完整文件（全部是 md，不需外網）。用於在專案裡做 AI 聊天介面、agent 介面、copilot 側欄、客服泡泡，或程式碼出現 @assistant-ui/react、components/assistant-ui/elements 時。"
---

# assistant-ui elements：何時用哪個

**這個 skill 用在沒有外網的環境。** 不要叫 agent 連 assistant-ui.com、不要跑
`npx shadcn add @assistant-ui/...` 或 `npx assistant-ui init`，那些都要外網。需要的東西都在 skill 裡
（以下路徑相對於本 skill 目錄 `~/.claude/skills/assistant-ui-elements/`）：

| 要什麼 | 在哪裡 |
|---|---|
| 某個 element 的安裝指令與最小範例 | `references/elements/<slug>.md` |
| 某個 element 的完整文件（API reference、Anatomy、所有範例） | `references/pages/<slug>.md` |
| runtime、tools、primitives、guides 等 React 文件 | `references/docs/`（例如 `references/docs/runtimes/`、`references/docs/tools/`） |
| 元件原始碼（base 與 radix 兩套），一個元件一個 md | `source/<flavor>/aui/<name>.md`、`source/<flavor>/ui/<name>.md` |

資料抓取日期：2026-11-14。

## 選用流程

1. **先決定要不要 runtime。**
   - 有 runtime（AI SDK、LangGraph、自訂 backend 接到 `AssistantRuntimeProvider`）→ 用 runtime 版，檔名 `<name>.aui.tsx`，原始碼 `source/<flavor>/aui/<name>.md`。
   - 沒有 runtime、只是要畫 UI（資料自己用 props 給）→ 用 standalone 版，檔名 `<name>.tsx`，原始碼 `source/<flavor>/aui/elements-<name>.md`。
   - 122 個兩種都有；只有 runtime：`thread`、`assistant-modal`、`assistant-sidebar`；只有 standalone：`agent-handoff`、`checkpoint-history`、`schedule-card`。
2. **先看 `Thread` 是否已經內建。** 裝了 `thread` 就已有：markdown、reasoning 摺疊、tool fallback、tool group、圖片、檔案、附件、分支切換、原地編輯訊息、follow-up suggestions、完整 composer。這些只在「不用 Thread、自己組版面」或「要換掉內建版本」時才另裝。
3. **在下面的決策表找情境**，再讀 `references/elements/<slug>.md` 拿要讀的原始碼檔與範例。
4. tool 專屬 UI 放在 toolkit entry 的 `render`（`defineToolkit`），不要靠覆寫 `ToolFallback`。

## 共通設定

### 離線安裝（照 md 手動寫檔）

1. **選 flavor**：看專案的 `components.json` 的 `style`。以 `base-` 開頭（例如 `base-nova`）用 `base`，其他（例如 `new-york`）用 `radix`。沒有 `components.json` 時，看 `components/ui/button.tsx` import 的是 `@base-ui/react` 還是 `radix-ui`／`@radix-ui/*`。
2. **找原始碼檔**：`references/elements/<slug>.md` 的安裝行寫了要讀哪個檔。runtime 版是 `source/<flavor>/aui/<slug>.md`，standalone 版是 `source/<flavor>/aui/elements-<slug>.md`。
3. **讀「完整安裝順序」那一行**：它列出要處理的所有項目（依賴在前，含本項），例如 `ui/utils`, `ui/button`, `aui/thread-list`。每一項對應 `source/<flavor>/<項目>.md`。
4. **逐項寫檔**：每個 md 的 `` ## 檔案：`<路徑>` `` 下面的 code block 就是檔案內容，原樣寫到專案的 `<路徑>`。專案有 `src/` 時寫在 `src/<路徑>`。
   - 檔案已存在就跳過，不要覆蓋（尤其是 `components/ui/*` 和 `lib/utils.ts`）。使用者要求才覆蓋。
   - 內容已經照 shadcn CLI 改寫過（`@/components/ui/*` 路徑、`lucide-react` 圖示），不用再改。
5. **裝 npm 套件**：用本項 md 的「npm（整組）」那行（已含所有依賴需要的套件）。有「npm dev（整組）」也一起裝。這些套件要從公司的 npm 鏡像裝；鏡像沒有某個套件時，回報給使用者，不要改去連外網。
6. **手動設定**：md 裡有「要手動加的 cssVars／css／tailwind」段落時，把 JSON 內容加進專案的 CSS／Tailwind 設定。

注意事項：

- 打包的 shadcn 基礎元件來自 `new-york-v4`（radix）與 `base-nova`（base）。專案用別的 style 時，基礎元件外觀可能不同；專案已有的基礎元件會跳過，所以通常沒差。
- 新版 shadcn 的 `lib/utils.ts` 是 `export { cn } from "cn"`（npm 套件 `cn`）。專案原本的 `lib/utils.ts`（`clsx` + `tailwind-merge`）也能用，不用換；這時 npm 指令裡的 `cn` 可以不裝。
- `markdown-text` 會 import `@assistant-ui/react-markdown/styles/dot.css`。Next.js、Vite 會處理；單獨跑 `tsc` 時要有 `declare module "*.css";`。

所有 runtime 版 element 都要有 provider 祖先：

```tsx
import { AssistantRuntimeProvider, AuiConfig, Suggestions } from "@assistant-ui/react";
import { useChatRuntime } from "@assistant-ui/ai-sdk";

function App({ children }: { children: React.ReactNode }) {
  const runtime = useChatRuntime();
  const config = AuiConfig({
    suggestions: Suggestions(["What's the weather?", "Tell me a joke"]),
  });
  return (
    <AssistantRuntimeProvider runtime={runtime} config={config}>
      {children}
    </AssistantRuntimeProvider>
  );
}
```

`config` 是選用的。`useChatRuntime` 來自 `@assistant-ui/ai-sdk`，要接 Vercel AI SDK 的 backend。公司內部 LLM 若是自己的 API，看 `references/docs/runtimes/`（例如 `custom/`、`langgraph/`）選 runtime。共用樣式 token（`paper`、`ghostButton`、`inkButton`、`field`、`mono`）在 `@/components/assistant-ui/elements/surfaces`，改它會影響所有 element。

## 三個最常見的起手式

**整頁聊天**（`thread`）：

```tsx
import { Thread } from "@/components/assistant-ui/elements/thread.aui";

export default function Chat() {
  return (
    <div className="h-full">
      <Thread />
    </div>
  );
}
```

換掉內建部件用 `components`（定義在 module scope 或 memoize）：`AssistantMessage`、`Welcome`、`ToolFallback`、`ToolGroup`、`ReasoningGroup`、`TaskGroup`、`ComposerExtra`。

**網站角落客服泡泡**（`assistant-modal`，整個 app 只放一次）：

```tsx
import { AssistantModal } from "@/components/assistant-ui/elements/assistant-modal.aui";

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <>
      {children}
      <AssistantModal />
    </>
  );
}
```

**App 旁邊的 copilot 側欄**（`assistant-sidebar`，左邊是你的 app，右邊是 Thread）：

```tsx
import { AssistantSidebar } from "@/components/assistant-ui/elements/assistant-sidebar.aui";

export default function Layout({ children }: { children: React.ReactNode }) {
  return <AssistantSidebar>{children}</AssistantSidebar>;
}
```

## 決策表

格式：`slug` — 什麼時候用。相近的放在一起，括號說明差別。

### 版面與外殼

- `thread` — 要一個完整聊天畫面（訊息、composer、自動捲動、歡迎畫面）。預設選它。
- `assistant-modal` — 嵌在既有網站角落的浮動聊天泡泡（客服、help desk）。
- `launcher-bubble` — 只要浮動入口按鈕和它展開的面板，自己控制內容（比 `assistant-modal` 更可自訂）。
- `assistant-sidebar` — 主畫面旁的可調寬 copilot 側欄。
- `canvas-split` — 產出長文件時，thread 讓位、文件佔主畫面，邊寫邊看。
- `chat-panel` — 想看「訊息、等待、串流回覆」整組怎麼拼；自己組聊天面板時的參考（`Thread` 已是完整版）。
- `message-pair` — 只要一組 user 泡泡加串流中的 assistant 回覆，hover 出動作列。
- `mobile-composer` — 手機版底部輸入框（避開鍵盤、大按鈕、快捷動作）。
- `empty-state` — 第一次進來的畫面：招呼語、三個起手提示、composer 置中。
- `onboarding` — 首次使用教學，三步說明這個助理能做什麼。
- `shared-conversation` — 別人分享過來的唯讀對話，附「接著聊」的入口。

### 對話列表與歷史

- `thread-list` — 切換對話的面板（新增、搜尋、依日期分組、改名、封存、刪除）。
- `thread-list-sidebar` — 要整個可收合側欄殼，左邊對話列表、右邊目前對話（`thread-list` 只是裸面板）。
- `thread-search` — 找回舊對話：釘選在前，再依時間分組。
- `conversation-search` — 在單一長對話裡搜尋，捲軸上標出命中位置。
- `day-separator` — 長對話裡的日期分隔線，hover 看時間。
- `draft-restore` — 回到對話時恢復沒送出的草稿。
- `checkpoint-history` — 可回溯的檢查點清單，標出每個點能還原什麼。

### 輸入框（composer）

- `composer` — 自己組輸入列，或 Thread 沒涵蓋的位置（例如精簡的 inline 輸入框）。用 Thread 時不需要。
- `composer-attachments` — 輸入框內暫存檔案並顯示每個檔案的上傳進度。
- `attachment` — runtime 附件元件（預覽、進度、移除），composer 與訊息都能用。
- `composer-slash-commands` — 打 `/` 叫出指令選單。
- `composer-mentions` — 打 `@` 拉入人或 agent。
- `composer-trigger-popover` — 自訂觸發字元的選單（mentions、slash 的底層，或巢狀動作）。
- `directive-text` — 把訊息中的 mention 指令渲染成 inline chip（搭配 mentions）。
- `composer-model-picker` — 模型選擇放在輸入框的工具列上。
- `model-selector` — 獨立的可搜尋模型選單，依 provider 分組，含 reasoning effort。
- `reasoning-effort` — 選「想多深」，並顯示這次實際用掉多少預算。
- `composer-voice` — 麥克風聽寫：輸入框變波形，結束後轉成文字。
- `composer-context` — 輸入框上的 token 圓環，接近上限時警告。
- `context-display` — context 用量（圓環、長條或文字），hover 看細節。
- `context-breakdown` — context 用在哪：prompt、tools、files、對話、剩餘。
- `message-queue` — run 進行中又打的訊息，排隊顯示、可取消。
- `prompt-library` — 存好的 prompt，可搜尋，插入前顯示變數。
- `quote` — 選取訊息文字、浮動工具列「引用」帶進輸入框。
- `command-palette` — 全 app 的指令面板（鍵盤叫出、依作用區分組）。

### 訊息內容渲染

- `markdown-text` — assistant 的 markdown（標題、清單、表格、程式碼）。Thread 已內建。
- `syntax-highlighter` — Prism 程式碼高亮，給 markdown 的 code block。
- `shiki-highlighter` — Shiki 高亮，等該段 part 串流結束才 tokenize（品質較好，較重）。
- `streaming-text` — 串流文字的淡入效果（新字藍色、再沉成墨色）。
- `math-block` — 數學式，逐步顯示推導。
- `mermaid-diagram` — 訊息中的 Mermaid 圖，支援串流中的不完整輸入。
- `diagram` — 已渲染好的圖（你給 graphic），提供縮放、重設、全螢幕。
- `image` — 圖片 part（預覽、載入、全螢幕）。Thread 已內建。
- `image-gallery` — 多張圖片結果的網格，共用 lightbox。
- `image-generation` — 生圖中：點陣佔位，圖從模糊中浮現。
- `file` — 檔案 part（類型圖示、播放、檔名大小、下載）。Thread 已內建。
- `media-player` — 小型音訊/影片播放器。
- `logos` — OpenAI、Anthropic、Google 的 provider 圖示。
- `speaker-identity` — 對話中不只一個模型或多人時，標示誰在說話。
- `generative-ui` — 渲染 spec 驅動的生成式 UI 元件庫輸出。

### 結構化結果（tool 回傳的資料）

- `data-table` — 可排序的小表格。
- `spec-sheet` — 單一物件的標籤式欄位清單（表格之外最常見的結構化答案）。
- `chart` — 面積、折線、長條圖，資料點隨串流逐一出現。
- `heat-graph` — 熱度圖（月份、星期、圖例、tooltip）。
- `activity-graph` — 半年份的 run 次數日曆格。
- `number-ticker` — 數字即時滾動更新。
- `timeline` — 時間軸事件，區分已發生與預計。
- `geo-map` — 座標轉成地圖、路線，並附可存取的地點清單。
- `map-answer` — 地點類答案：圖釘、路線、來源清單。
- `comparison-card` — 兩個選項並排比較，標出建議並說明理由。
- `score-breakdown` — 評分結果加上計算過程（準則、權重、扣分原因）。
- `recommendation-card` — agent 提出修改建議與信心值，等使用者同意。
- `link-preview` — 回傳 URL 的小卡片。
- `web-preview` — 沙盒預覽外框（網址列、重新整理、新分頁開啟）。
- `research-report` — 研究報告大綱逐節填入，每節附來源。
- `artifact-card` — 生成的文件做成卡片，即時寫入、有版本。
- `file-tree` — 一次 run 動到的檔案樹，附每檔增刪行數。
- `code-diff` — 聊天用的 unified diff（只看）。
- `reviewable-diff` — 每個 hunk 可保留或丟棄，再套用剩下的（要使用者審）。
- `code-runner` — 程式片段加執行鈕，輸出附在下面。
- `terminal-block` — 逐行串流的指令輸出，結尾附 exit status。

### 引用與可信度

- `sources` — runtime 的引用 chip 與來源卡片。一般 RAG 回答用這個。
- `inline-citation` — 句子旁的編號引用標記。
- `document-reference` — 回答依據的文件：引用段落與跳到頁碼。
- `retrieval-chunks` — 檢索到的段落與分數，在答案之前顯示。
- `confidence-marker` — 標出哪些說法有來源、哪些是推論、哪些是猜測。
- `web-search` — 搜尋查詢與結果逐筆出現。

### Tool call 與 agent 執行過程

- `tool-fallback` — 沒有專屬 UI 的 tool call 預設卡片（摺疊一行、展開看參數結果、等待核准時有按鈕）。Thread 已內建。
- `tool-call` — 單一 tool 呼叫，請求與結果收在摺疊內；自己寫 tool UI 的基底。
- `tool-group` — 同一回合連續多個 tool call 包成一組摺疊。Thread 已內建。
- `tool-error` — 單一 tool 失敗：錯誤、重試次數、只重試這一步不重跑整回合。
- `tool-timeline` — 整段工作摘要成「動詞、目標、檔案統計」。
- `run-activity` — run 進行中的精簡摘要，依序列出說明與 tool，最後答案保持可見。
- `reasoning` — assistant 推理過程的摺疊區。Thread 已內建。
- `thinking-indicator` — 「正在做什麼」的即時狀態列，附經過時間。
- `typing-indicator` — 經典三個點。
- `loading-state` — 模型還沒有輸出時的像素動畫。
- `agent-status` — 一顆狀態膠囊：在做什麼、做多久。
- `agent-plan` — agent 依序完成的計畫清單與進度。
- `todo-list` — agent 自己的待辦清單，執行中會改寫。
- `job-progress` — 有權重的多階段工作進度，結束後變成收據。
- `task-card` — 委派出去的任務卡：狀態、時間、結果、子對話。
- `subagent-list` — 平行的子 worker，各自的進度、模型、完成狀態。
- `agent-handoff` — 控制權從一個 agent 交給另一個，附原因與帶過去的東西。
- `agent-card` — 要對話的 agent 介紹：技能、模型、endpoint。
- `flow-graph` — 工作流程圖，分支展開再匯合。
- `trace-waterfall` — run 的所有 span 在同一時間軸上，看時間花在哪。
- `computer-use` — agent 操作的螢幕畫面、游標軌跡、目前動作。
- `background-inbox` — 背景還在跑的工作，與等待收取的結果。
- `schedule-card` — 定期執行的 run：頻率與過往表現。
- `connection-state` — 連線中斷、server 端繼續跑、恢復串流。
- `stopped-run` — 使用者按停止後，保留半段回答，一鍵繼續。

### 人工介入（human in the loop）

- `approval-card` — agent 執行重大動作前先問（允許一次、永遠允許、拒絕）。
- `permission-grant` — 授予一項能力（不是核准單一動作），說明權限範圍。
- `option-list` — agent 提問並給幾個選項，選擇回傳給 agent 並留下紀錄。
- `question-flow` — 依序問幾個短問題，最後一次回傳全部答案。
- `elicitation-form` — MCP server 在 tool call 中途要使用者補欄位。
- `guardrail-notice` — 拒絕時的專屬樣式，附最接近的替代做法。

### 訊息動作與分支

- `message-actions` — 複製、評分、重新產生。
- `message-branches` — 在重新產生的多個版本間切換。Thread 已內建。
- `edit-message` — 原地改寫一則訊息成新分支，預先告知舊分支留下幾則回覆。Thread 已內建（不含分支提示）。
- `regenerate-menu` — 用別的模型重跑同一回合。
- `feedback-dialog` — 按倒讚後詢問原因。
- `follow-up-suggestions` — runtime 產生的後續提問 chip。Thread 已內建。
- `message-timing` — 這則訊息的首字延遲、總時間、速度。
- `read-aloud` — 朗讀回答，逐字高亮，可調速度。
- `scroll-anchor` — 串流時不搶捲動位置，提供「回到底部」按鈕。
- `tooltip-icon-button` — 有 tooltip 的無障礙圖示按鈕（其他 element 的共用零件）。

### 語音

- `orb` — 即時語音的圓球，含連線、靜音、說話狀態。
- `voice-conversation` — 完整通話畫面：圓球、字幕、逐字稿。
- `composer-voice` — 只要把語音聽寫成文字（不是對話）。

### 設定、用量、狀態

- `settings-panel` — 模型、system prompt、temperature、權限設定。
- `mcp-config` — 新增 connector 與自訂 MCP server（含驗證與連線狀態）。
- `mcp-server-panel` — 已連的 server、各自提供什麼、哪個在等使用者。
- `memory-chips` — 這回合記住了使用者什麼，可移除。
- `cost-meter` — 這次 run 花多少，依模型拆，對照整個 session。
- `quota-banner` — 剩多少額度、何時恢復、怎麼加購。
- `error-state` — 低調的錯誤橫幅與重試，不跳 modal。

## 常見混淆

- `thread-list` vs `thread-list-sidebar` vs `thread-search`：面板 / 含外殼的側欄 / 找舊對話。
- `assistant-modal` vs `launcher-bubble` vs `assistant-sidebar`：固定組合的泡泡 / 可自訂內容的泡泡 / 分割畫面。
- `composer-model-picker` vs `model-selector`：放在輸入框工具列 / 獨立選單（含 reasoning effort）。
- `context-display` vs `composer-context` vs `context-breakdown`：通用用量顯示 / 輸入框上的圓環 / 用量拆解。
- `syntax-highlighter` vs `shiki-highlighter`：Prism 輕量 / Shiki 較精準、等串流結束才上色。
- `code-diff` vs `reviewable-diff`：只看 / 逐 hunk 決定。
- `tool-fallback` vs `tool-call` vs `tool-group`：預設卡 / 自寫 tool UI 的基底 / 多個 tool 的外框。
- `approval-card` vs `permission-grant` vs `option-list`：核准單一動作 / 授予長期能力 / 讓使用者選答案。
- `diagram` vs `mermaid-diagram`：你給已渲染的圖 / 由 Mermaid 原始碼渲染。
- `geo-map` vs `map-answer`：通用地圖與地點清單 / 回答型的圖釘加路線。

## 更新參考檔

內容是 2026-11-14 從 assistant-ui.com 抓的。這個 skill 只有 md，不含更新用的腳本。要更新時，在有外網的機器上請 Claude 重抓 `references/pages/`、`references/docs/`、`source/`，並重建 `references/elements/`，再把整個 skill 目錄帶進內網。新增或刪除的 element 要補進上面的決策表。
