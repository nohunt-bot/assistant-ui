# @assistant-ui/react-data-stream
URL: /docs/api-reference/integrations/react-data-stream

Data stream runtime hook and message conversion utilities for custom assistant-ui streaming backends.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### useCloudRuntime

- `options`: `UseCloudRuntimeOptions`

  - `onError?`: `(error: Error) => void`

  - `onCancel?`: `() => void`

  - `adapters?`: `Omit<LocalRuntimeOptionsBase["adapters"], "chatModel"> | undefined`

    - `attachments?`: `AttachmentAdapter | undefined`

      - `accept`: `string`
      - `add`: `(state: { file: File; }) => Promise<PendingAttachment> | AsyncGenerator<PendingAttachment, void>` — Returns the pending attachment for the file, or an async generator yielding successive states of that one attachment. Every yield must have the same id.
      - `remove`: `(attachment: Attachment) => Promise<void>`
      - `send`: `(attachment: PendingAttachment, options?: { signal?: AbortSignal; }) => Promise<CompleteAttachment>`

    - `suggestion?`: `SuggestionAdapter | undefined`

      - `key?`: `string | number | symbol | undefined` — Changes when the adapter should regenerate suggestions for a settled run.
      - `generate`: `( options: SuggestionAdapterGenerateOptions, ) => | Promise<readonly ThreadSuggestion[]> | AsyncGenerator<readonly ThreadSuggestion[], void>`

    - `voice?`: `RealtimeVoiceAdapter | undefined`
      - `connect`: `(options: { abortSignal?: AbortSignal; }) => RealtimeVoiceAdapter.Session`

    - `dictation?`: `DictationAdapter | undefined`

      - `listen`: `() => DictationAdapter.Session`
      - `disableInputDuringDictation?`: `boolean`

    - `speech?`: `SpeechSynthesisAdapter | undefined`
      - `speak`: `(text: string) => SpeechSynthesisAdapter.Utterance`

    - `feedback?`: `FeedbackAdapter | undefined`
      - `submit`: `(feedback: FeedbackAdapterFeedback) => void`

    - `history?`: `ThreadHistoryAdapter | undefined`

      - `scopeId?`: `string | undefined` — Stable identity for the storage scope read by LocalRuntime. Keep it the same when recreating the adapter for one thread, account, or workspace, and change it before loading a different scope. Adapters that omit it are treated as sharing one scope. External-history runtimes do not read it.
      - `unstable_copy?`: `(( branch: readonly ThreadMessage[], messageIds: readonly string[], ) => Promise<void>) | undefined` (experimental) — Keeps a copy of messages whose source of truth is the runtime's backend. \`branch\` is the conversation from its first message to its last, and \`messageIds\` names the ones in it that are new or changed; the adapter stores those, and first any earlier message of the branch it does not hold yet, keyed by each message's own id. It is undefined while the adapter keeps no copies, so the runtime then neither copies nor records tool interactions.
      - `load`: `() => Promise<ExportedMessageRepository & { state?: ReadonlyJSONValue; unstable_resume?: boolean; }>`
      - `resume?`: `(options: ChatModelRunOptions) => AsyncGenerator<ChatModelRunResult, void, unknown>`
      - `append`: `(item: ExportedMessageRepositoryItem) => Promise<void>`
      - `update?`: `(item: ExportedMessageRepositoryItem) => Promise<void>` — Rewrites a previously appended message in place, keyed by its message id. Adapters that implement this let a runtime persist a run paused for tool approval, finalize the same message once the run resumes, and record a tool result that arrives after the message settled, which can be a message later turns follow. Without it, a paused run is appended when it ends or when a later turn follows its message while the run is still open; anything that run adds after the append is not stored. An update may arrive for an id whose earlier write failed; treat it as an upsert keyed on the message id rather than assuming the entry exists.
      - `delete?`: `(items: ExportedMessageRepositoryItem[]) => Promise<void>` — Deletes messages from history. The runtime may send a second delete for the same message after a write it issued before the delete lands; treat deleting a missing entry as success.
      - `withFormat?`: `<TMessage, TStorageFormat extends Record<string, unknown>>(formatAdapter: MessageFormatAdapter<TMessage, TStorageFormat>) => GenericThreadHistoryAdapter<TMessage>` — Required when used with \`useAISDKRuntime\` / \`useChatRuntime\`.

  - `maxSteps?`: `number | undefined`

  - `unstable_humanToolNames?`: `string[] | undefined` (experimental) — Names of tools that pause the run until a result is supplied via \`addToolResult\`.

  - `unstable_enableMessageQueue?`: `boolean | undefined` (experimental) — Opt in to message queuing: a message sent during a run is held in \`composer.queue\` and sent once the run settles. Steering runs it next.

  - `unstable_queueClearOnRewind?`: `boolean | undefined` (deprecated: Removal after 2026-11-05 — the queue will always survive rewinds.) — Auto-clear the message queue when the thread rewinds (message edit). Defaults to \`true\`.

  - `unstable_queueClearOnCancel?`: `boolean | undefined` (deprecated: Removal after 2026-11-05 — cancel will always pause the queue and keep the items.) — Auto-clear the message queue when the user cancels the run. Defaults to \`true\`. When \`false\`, cancel pauses the queue and keeps the pending items; the next send drains them.

  - `cloud`: `AssistantCloud | undefined`

    - `threads`: `AssistantCloudThreads`

      - `messages`: `AssistantCloudThreadMessages`
      - `runs`: `AssistantCloudThreadRuns`
      - `cloud`: `AssistantCloudAPI`
      - `list`: `(query?: AssistantCloudThreadsListQuery) => Promise<AssistantCloudThreadsListResponse>`
      - `get`: `(threadId: string) => Promise<CloudThread>`
      - `create`: `(body: AssistantCloudThreadsCreateBody) => Promise<AssistantCloudThreadsCreateResponse>`
      - `update`: `(threadId: string, body: AssistantCloudThreadsUpdateBody) => Promise<void>`
      - `claim`: `(body: AssistantCloudThreadsClaimBody) => Promise<AssistantCloudThreadsClaimResponse>` — Moves every thread of the anonymous identity behind \`refresh_token\` into the caller's workspace.
      - `delete`: `(threadId: string) => Promise<void>`

    - `projects`: `AssistantCloudProjects`
      - `threads`: `AssistantCloudProjectThreads`

    - `auth`: `__object`

      - `tokens`: `AssistantCloudAuthTokens`
      - `invalidate`: `() => void`

    - `runs`: `AssistantCloudRuns`

      - `cloud`: `AssistantCloudAPI`
      - `stream`: `(body: AssistantCloudRunsStreamBody) => Promise<AssistantStream>`
      - `report`: `(body: AssistantCloudRunReport) => Promise<{ run_id: string; }>`

    - `files`: `AssistantCloudFiles`

      - `cloud`: `AssistantCloudAPI`
      - `pdfToImages`: `(body: PdfToImagesRequestBody) => Promise<PdfToImagesResponse>` (deprecated: Assistant Cloud has no PDF conversion endpoint, so this request always rejects with a \`CloudAPIError\` whose \`status\` is 404.)
      - `generatePresignedUploadUrl`: `(body: GeneratePresignedUploadUrlRequestBody) => Promise<GeneratePresignedUploadUrlResponse>`
      - `generatePresignedDownloadUrl`: `(body: { key: string; } | { url: string; }) => Promise<GeneratePresignedDownloadUrlResponse>`

    - `events`: `AssistantCloudEvents`

      - `buffer`: `AssistantCloudEvent[]`
      - `timer?`: `ReturnType<typeof setTimeout> | undefined`
      - `flushing?`: `Promise<void> | undefined`
      - `retryTimer?`: `ReturnType<typeof setTimeout> | undefined`
      - `resolveRetryDelay?`: `(() => void) | undefined`
      - `bestEffortRequested`: `boolean`
      - `generation`: `number`
      - `cloud`: `AssistantCloudAPI`
      - `isEnabled`: `() => boolean`
      - `listening`: `boolean`
      - `track`: `(event: AssistantCloudEvent) => void`
      - `listen`: `() => void`
      - `unlisten`: `() => void`
      - `dispose`: `() => void`
      - `clearPending`: `() => void`
      - `onVisibilityChange`: `() => void`
      - `flushBestEffort`: `() => Promise<void>`
      - `flush`: `(retryFailures: boolean) => Promise<void>`
      - `flushPending`: `(retryFailures: boolean) => Promise<void>`
      - `waitForRetry`: `(delay: number) => Promise<void>`
      - `interruptRetryDelay`: `() => void`
      - `scheduleFlush`: `() => void`
      - `clearFlushTimer`: `() => void`

    - `scores`: `AssistantCloudScores`

      - `cloud`: `AssistantCloudAPI`
      - `create`: `(body: AssistantCloudScoreBody) => Promise<AssistantCloudScoreResponse>`

    - `telemetry`: `AssistantCloudTelemetryConfig`

      - `enabled?`: `boolean` — Enables Assistant Cloud telemetry. Defaults to \`true\`. Set to \`false\` to disable both run reports and engagement events.
      - `events?`: `boolean` — Enables Assistant Cloud engagement events. Defaults to \`true\` when telemetry is enabled. Set to \`false\` to keep run reports while disabling engagement events.
      - `messages?`: `boolean` — Stores the messages of runtimes whose backend keeps the transcript (LangGraph, LangChain, Google ADK, custom external stores), so the dashboard can show them. Defaults to \`true\` when telemetry is enabled. Set to \`false\` to keep run reports and events without storing those messages.
      - `release?`: `string`
      - `environment?`: `string`
      - `tags?`: `string[]`
      - `beforeReport?`: `( report: AssistantCloudRunReport, ) => AssistantCloudRunReport | null` — Called before each telemetry report is sent. Return a modified report to enrich it (e.g. add \`model_id\`), or return \`null\` to skip the report.

    - `registerSdk`: `(sdk: SdkIdentity) => void`

  - `maxStreamLineLength?`: `number | undefined` — Maximum UTF-16 code units accepted in one data-stream or SSE line. Defaults to 16 MiB.

  - `maxStreamEventLength?`: `number | undefined` — Maximum UTF-16 code units retained across one UI-message-stream SSE event. Defaults to 16 MiB.

  - `onResponse?`: `(response: Response) => void | Promise<void>`

  - `onFinish?`: `(message: ThreadMessage) => void`

  - `scopeId?`: `string | undefined` — Stable identity for the account or workspace owning Cloud runtime state. Provide it from the first render and change it when that scope changes.

  - `initialMessages?`: `readonly ThreadMessageLike[] | undefined` — A message without an id gets a generated id, and a message without createdAt is stamped with the current time. Set createdAt on each initial message when prerendering a page with Next.js cacheComponents. With a history adapter, they survive an empty load, and those still in the thread are written to history before the first write for another message or when a run settles one of them, under the ids they carry, so a history adapter that keys messages across threads needs ids that differ per thread. A non-empty load replaces them.

  - `onData?`: `(data: { type: string; name: string; data: unknown; transient?: boolean; }) => void` — Callback for data-\* parts (ui-message-stream only).

  - `credentials?`: `RequestCredentials`

  - `sendExtraMessageFields?`: `boolean`

  - `assistantId`: `string`

### useDataStreamRuntime

- `options`: `UseDataStreamRuntimeOptions`

  - `api`: `string`

  - `protocol?`: `DataStreamProtocol` — Defaults to response-header detection, then "ui-message-stream".

  - `onData?`: `(data: { type: string; name: string; data: unknown; transient?: boolean; }) => void` — Callback for data-\* parts (ui-message-stream only).

  - `onResponse?`: `(response: Response) => void | Promise<void>`

  - `onFinish?`: `(message: ThreadMessage) => void`

  - `onError?`: `(error: Error) => void`

  - `onCancel?`: `() => void`

  - `credentials?`: `RequestCredentials`

  - `headers?`: `HeadersValue | (() => Promise<HeadersValue>)`

    - `append`: `{ (name: string, value: string): void; (name: string, value: string): void; }` — The \*\*\`append()\`\*\* method of the Headers interface appends a new value onto an existing header inside a Headers object, or adds the header if it does not already exist. [MDN Reference](https://developer.mozilla.org/docs/Web/API/Headers/append)
    - `delete`: `{ (name: string): void; (name: string): void; }` — The \*\*\`delete()\`\*\* method of the Headers interface deletes a header from the current Headers object. [MDN Reference](https://developer.mozilla.org/docs/Web/API/Headers/delete)
    - `get`: `{ (name: string): string | null; (name: string): string | null; }` — The \*\*\`get()\`\*\* method of the Headers interface returns a byte string of all the values of a header within a Headers object with a given name. If the requested header doesn't exist in the Headers object, it returns null. [MDN Reference](https://developer.mozilla.org/docs/Web/API/Headers/get)
    - `getSetCookie`: `() => string[]` — The \*\*\`getSetCookie()\`\*\* method of the Headers interface returns an array containing the values of all Set-Cookie headers associated with a response. This allows Headers objects to handle having multiple Set-Cookie headers, which wasn't possible prior to its implementation. [MDN Reference](https://developer.mozilla.org/docs/Web/API/Headers/getSetCookie)
    - `has`: `{ (name: string): boolean; (name: string): boolean; }` — The \*\*\`has()\`\*\* method of the Headers interface returns a boolean stating whether a Headers object contains a certain header. [MDN Reference](https://developer.mozilla.org/docs/Web/API/Headers/has)
    - `set`: `{ (name: string, value: string): void; (name: string, value: string): void; }` — The \*\*\`set()\`\*\* method of the Headers interface sets a new value for an existing header inside a Headers object, or adds the header if it does not already exist. [MDN Reference](https://developer.mozilla.org/docs/Web/API/Headers/set)
    - `forEach`: `{ (callbackfn: (value: string, key: string, parent: Headers) => void, thisArg?: any): void; (callbackfn: (value: string, key: string, parent: Headers) => void, thisArg?: any): void; }`
    - `entries`: `{ (): HeadersIterator<[string, string]>; (): IterableIterator<[string, string]>; }` — Returns an iterator allowing to go through all key/value pairs contained in this object.
    - `keys`: `{ (): HeadersIterator<string>; (): IterableIterator<string>; }` — Returns an iterator allowing to go through all keys of the key/value pairs contained in this object.
    - `values`: `{ (): HeadersIterator<string>; (): IterableIterator<string>; }` — Returns an iterator allowing to go through all values of the key/value pairs contained in this object.

  - `body?`: `object | ((options: DataStreamRuntimeBodyOptions) => Promise<object | undefined>)` — Extra request body fields; a callback receives the active remote thread id.

  - `sendExtraMessageFields?`: `boolean`

  - `maxStreamLineLength?`: `number | undefined` — Maximum UTF-16 code units accepted in one data-stream or SSE line. Defaults to 16 MiB.

  - `maxStreamEventLength?`: `number | undefined` — Maximum UTF-16 code units retained across one UI-message-stream SSE event. Defaults to 16 MiB.

  - `maxSteps?`: `number | undefined`

  - `unstable_humanToolNames?`: `string[] | undefined` (experimental) — Names of tools that pause the run until a result is supplied via \`addToolResult\`.

  - `unstable_enableMessageQueue?`: `boolean | undefined` (experimental) — Opt in to message queuing: a message sent during a run is held in \`composer.queue\` and sent once the run settles. Steering runs it next.

  - `unstable_queueClearOnRewind?`: `boolean | undefined` (deprecated: Removal after 2026-11-05 — the queue will always survive rewinds.) — Auto-clear the message queue when the thread rewinds (message edit). Defaults to \`true\`.

  - `unstable_queueClearOnCancel?`: `boolean | undefined` (deprecated: Removal after 2026-11-05 — cancel will always pause the queue and keep the items.) — Auto-clear the message queue when the user cancels the run. Defaults to \`true\`. When \`false\`, cancel pauses the queue and keeps the pending items; the next send drains them.

  - `cloud?`: `AssistantCloud | undefined`

    - `threads`: `AssistantCloudThreads`

      - `messages`: `AssistantCloudThreadMessages`
      - `runs`: `AssistantCloudThreadRuns`
      - `cloud`: `AssistantCloudAPI`
      - `list`: `(query?: AssistantCloudThreadsListQuery) => Promise<AssistantCloudThreadsListResponse>`
      - `get`: `(threadId: string) => Promise<CloudThread>`
      - `create`: `(body: AssistantCloudThreadsCreateBody) => Promise<AssistantCloudThreadsCreateResponse>`
      - `update`: `(threadId: string, body: AssistantCloudThreadsUpdateBody) => Promise<void>`
      - `claim`: `(body: AssistantCloudThreadsClaimBody) => Promise<AssistantCloudThreadsClaimResponse>` — Moves every thread of the anonymous identity behind \`refresh_token\` into the caller's workspace.
      - `delete`: `(threadId: string) => Promise<void>`

    - `projects`: `AssistantCloudProjects`
      - `threads`: `AssistantCloudProjectThreads`

    - `auth`: `__object`

      - `tokens`: `AssistantCloudAuthTokens`
      - `invalidate`: `() => void`

    - `runs`: `AssistantCloudRuns`

      - `cloud`: `AssistantCloudAPI`
      - `stream`: `(body: AssistantCloudRunsStreamBody) => Promise<AssistantStream>`
      - `report`: `(body: AssistantCloudRunReport) => Promise<{ run_id: string; }>`

    - `files`: `AssistantCloudFiles`

      - `cloud`: `AssistantCloudAPI`
      - `pdfToImages`: `(body: PdfToImagesRequestBody) => Promise<PdfToImagesResponse>` (deprecated: Assistant Cloud has no PDF conversion endpoint, so this request always rejects with a \`CloudAPIError\` whose \`status\` is 404.)
      - `generatePresignedUploadUrl`: `(body: GeneratePresignedUploadUrlRequestBody) => Promise<GeneratePresignedUploadUrlResponse>`
      - `generatePresignedDownloadUrl`: `(body: { key: string; } | { url: string; }) => Promise<GeneratePresignedDownloadUrlResponse>`

    - `events`: `AssistantCloudEvents`

      - `buffer`: `AssistantCloudEvent[]`
      - `timer?`: `ReturnType<typeof setTimeout> | undefined`
      - `flushing?`: `Promise<void> | undefined`
      - `retryTimer?`: `ReturnType<typeof setTimeout> | undefined`
      - `resolveRetryDelay?`: `(() => void) | undefined`
      - `bestEffortRequested`: `boolean`
      - `generation`: `number`
      - `cloud`: `AssistantCloudAPI`
      - `isEnabled`: `() => boolean`
      - `listening`: `boolean`
      - `track`: `(event: AssistantCloudEvent) => void`
      - `listen`: `() => void`
      - `unlisten`: `() => void`
      - `dispose`: `() => void`
      - `clearPending`: `() => void`
      - `onVisibilityChange`: `() => void`
      - `flushBestEffort`: `() => Promise<void>`
      - `flush`: `(retryFailures: boolean) => Promise<void>`
      - `flushPending`: `(retryFailures: boolean) => Promise<void>`
      - `waitForRetry`: `(delay: number) => Promise<void>`
      - `interruptRetryDelay`: `() => void`
      - `scheduleFlush`: `() => void`
      - `clearFlushTimer`: `() => void`

    - `scores`: `AssistantCloudScores`

      - `cloud`: `AssistantCloudAPI`
      - `create`: `(body: AssistantCloudScoreBody) => Promise<AssistantCloudScoreResponse>`

    - `telemetry`: `AssistantCloudTelemetryConfig`

      - `enabled?`: `boolean` — Enables Assistant Cloud telemetry. Defaults to \`true\`. Set to \`false\` to disable both run reports and engagement events.
      - `events?`: `boolean` — Enables Assistant Cloud engagement events. Defaults to \`true\` when telemetry is enabled. Set to \`false\` to keep run reports while disabling engagement events.
      - `messages?`: `boolean` — Stores the messages of runtimes whose backend keeps the transcript (LangGraph, LangChain, Google ADK, custom external stores), so the dashboard can show them. Defaults to \`true\` when telemetry is enabled. Set to \`false\` to keep run reports and events without storing those messages.
      - `release?`: `string`
      - `environment?`: `string`
      - `tags?`: `string[]`
      - `beforeReport?`: `( report: AssistantCloudRunReport, ) => AssistantCloudRunReport | null` — Called before each telemetry report is sent. Return a modified report to enrich it (e.g. add \`model_id\`), or return \`null\` to skip the report.

    - `registerSdk`: `(sdk: SdkIdentity) => void`

  - `scopeId?`: `string | undefined` — Stable identity for the account or workspace owning Cloud runtime state. Provide it from the first render and change it when that scope changes.

  - `initialMessages?`: `readonly ThreadMessageLike[] | undefined` — A message without an id gets a generated id, and a message without createdAt is stamped with the current time. Set createdAt on each initial message when prerendering a page with Next.js cacheComponents. With a history adapter, they survive an empty load, and those still in the thread are written to history before the first write for another message or when a run settles one of them, under the ids they carry, so a history adapter that keys messages across threads needs ids that differ per thread. A non-empty load replaces them.

  - `adapters?`: `Omit<LocalRuntimeOptionsBase["adapters"], "chatModel"> | undefined`

    - `attachments?`: `AttachmentAdapter | undefined`

      - `accept`: `string`
      - `add`: `(state: { file: File; }) => Promise<PendingAttachment> | AsyncGenerator<PendingAttachment, void>` — Returns the pending attachment for the file, or an async generator yielding successive states of that one attachment. Every yield must have the same id.
      - `remove`: `(attachment: Attachment) => Promise<void>`
      - `send`: `(attachment: PendingAttachment, options?: { signal?: AbortSignal; }) => Promise<CompleteAttachment>`

    - `suggestion?`: `SuggestionAdapter | undefined`

      - `key?`: `string | number | symbol | undefined` — Changes when the adapter should regenerate suggestions for a settled run.
      - `generate`: `( options: SuggestionAdapterGenerateOptions, ) => | Promise<readonly ThreadSuggestion[]> | AsyncGenerator<readonly ThreadSuggestion[], void>`

    - `voice?`: `RealtimeVoiceAdapter | undefined`
      - `connect`: `(options: { abortSignal?: AbortSignal; }) => RealtimeVoiceAdapter.Session`

    - `dictation?`: `DictationAdapter | undefined`

      - `listen`: `() => DictationAdapter.Session`
      - `disableInputDuringDictation?`: `boolean`

    - `speech?`: `SpeechSynthesisAdapter | undefined`
      - `speak`: `(text: string) => SpeechSynthesisAdapter.Utterance`

    - `feedback?`: `FeedbackAdapter | undefined`
      - `submit`: `(feedback: FeedbackAdapterFeedback) => void`

    - `history?`: `ThreadHistoryAdapter | undefined`

      - `scopeId?`: `string | undefined` — Stable identity for the storage scope read by LocalRuntime. Keep it the same when recreating the adapter for one thread, account, or workspace, and change it before loading a different scope. Adapters that omit it are treated as sharing one scope. External-history runtimes do not read it.
      - `unstable_copy?`: `(( branch: readonly ThreadMessage[], messageIds: readonly string[], ) => Promise<void>) | undefined` (experimental) — Keeps a copy of messages whose source of truth is the runtime's backend. \`branch\` is the conversation from its first message to its last, and \`messageIds\` names the ones in it that are new or changed; the adapter stores those, and first any earlier message of the branch it does not hold yet, keyed by each message's own id. It is undefined while the adapter keeps no copies, so the runtime then neither copies nor records tool interactions.
      - `load`: `() => Promise<ExportedMessageRepository & { state?: ReadonlyJSONValue; unstable_resume?: boolean; }>`
      - `resume?`: `(options: ChatModelRunOptions) => AsyncGenerator<ChatModelRunResult, void, unknown>`
      - `append`: `(item: ExportedMessageRepositoryItem) => Promise<void>`
      - `update?`: `(item: ExportedMessageRepositoryItem) => Promise<void>` — Rewrites a previously appended message in place, keyed by its message id. Adapters that implement this let a runtime persist a run paused for tool approval, finalize the same message once the run resumes, and record a tool result that arrives after the message settled, which can be a message later turns follow. Without it, a paused run is appended when it ends or when a later turn follows its message while the run is still open; anything that run adds after the append is not stored. An update may arrive for an id whose earlier write failed; treat it as an upsert keyed on the message id rather than assuming the entry exists.
      - `delete?`: `(items: ExportedMessageRepositoryItem[]) => Promise<void>` — Deletes messages from history. The runtime may send a second delete for the same message after a write it issued before the delete lands; treat deleting a missing entry as success.
      - `withFormat?`: `<TMessage, TStorageFormat extends Record<string, unknown>>(formatAdapter: MessageFormatAdapter<TMessage, TStorageFormat>) => GenericThreadHistoryAdapter<TMessage>` — Required when used with \`useAISDKRuntime\` / \`useChatRuntime\`.

### toLanguageModelMessages

> [!warn]
>
> **Deprecated.** Use `toGenericMessages` from `assistant-stream` for framework-agnostic conversion. This function is kept for AI SDK compatibility.

- `messages`: `readonly ThreadMessage[]`
- `options?`: `{ unstable_includeId?: boolean | undefined; }`
  - `unstable_includeId?`: `boolean | undefined` (experimental)