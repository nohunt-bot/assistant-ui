# Runtime Hooks
URL: /docs/api-reference/hooks/runtimes

Runtime creation hooks for local, remote, cloud, external-store, and AI SDK powered assistant-ui chat experiences.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### useCloudThreadListRuntime

- `options`: `CloudThreadListAdapter`

  - `cloud`: `AssistantCloud`

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

  - `scopeId?`: `string | undefined`

  - `runtimeHook`: `() => AssistantRuntime`

  - `create?`: `(threadId: string) => Promise<ThreadData>`

  - `delete?`: `(threadId: string) => Promise<void>`

### useLocalRuntime

- `chatModel`: `ChatModelAdapter`
  - `run`: `(options: ChatModelRunOptions) => Promise<ChatModelRunResult> | AsyncGenerator<ChatModelRunResult, void>`

- `options?`: `LocalRuntimeOptions`

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

### useRemoteThreadListRuntime

- `options`: `RemoteThreadListOptions`

  - `runtimeHook`: `() => AssistantRuntime`

  - `adapter`: `RemoteThreadListAdapter` — The adapter reference should remain stable across renders. Replacing it reloads the list and drops cached threads that are not in the replacement page. In-flight mutations from the previous adapter are cancelled.

    - `list`: `(params?: RemoteThreadListPageOptions) => Promise<RemoteThreadListResponse>`
    - `rename`: `(remoteId: string, newTitle: string) => Promise<void>`
    - `updateCustom?`: `(remoteId: string, custom: Record<string, unknown> | undefined) => Promise<void>`
    - `archive`: `(remoteId: string) => Promise<void>`
    - `unarchive?`: `(remoteId: string) => Promise<void>` — Restores an archived thread. Omit it when the backend cannot unarchive: switching to an archived thread then opens it as archived, and an explicit unarchive rejects.
    - `delete`: `(remoteId: string) => Promise<void>`
    - `initialize`: `(threadId: string, options?: { custom?: Record<string, unknown> | undefined; }) => Promise<RemoteThreadInitializeResponse>` — Creates a thread with draft metadata passed in \`options.custom\`. Draft metadata reaches the backend only through this call, so an adapter that ignores \`options.custom\` drops it on the next \`list()\`.
    - `generateTitle`: `(remoteId: string, unstable_messages: readonly ThreadMessage[]) => Promise<AssistantStream>` — Generates a title for the thread and streams it back. Called automatically once the first message settles and, if the thread still has no title, again after its first run ends (completion, error, or cancellation). A backend that titles the thread asynchronously may keep the stream open until its title is ready. When the generation persists the title itself, the returned stream must not complete before that write has landed. Concurrent generations are ordered by stream completion, and a run whose write outlives its stream can overwrite a newer title.
    - `fetch`: `(threadId: string) => Promise<RemoteThreadMetadata>`
    - `unstable_Provider?`: `RemoteThreadListProviderComponent | undefined` (deprecated: Return the adapters from \`unstable_useAdapters\` instead. \`unstable_Provider\` will be removed in a future minor release of \`@assistant-ui/core\`.) — Optional React component wrapped around each active thread, an older face of \`unstable_useAdapters\` for injecting per-thread adapters such as history or attachments. A component that only wraps \`children\` in \`RuntimeAdapterProvider\` migrates by returning those adapters from \`unstable_useAdapters\`. \`useRemoteThreadListRuntime\` renders this component only when the adapter it mounts with has one, and that runtime then runs on the deprecated \`RemoteThreadListThreadListRuntimeCore\` instead of the \`RemoteThreadList\` client; it renders each later adapter's provider and synthesizes a \`RuntimeAdapterProvider\` from \`unstable_useAdapters\` for a later adapter without one. A hook that mounts with an adapter without this component calls \`unstable_useAdapters\` for every later adapter and ignores this component. The \`RemoteThreadList\` store entry ignores it. The Provider must render \`children\` on its first commit; deferring them behind a loading state, a Suspense boundary, or a \`useEffect\`-gated render is unsupported and leaves thread context unavailable to downstream consumers. Load data inside an always-mounted child instead.
    - `unstable_useAdapters?`: `(() => RuntimeAdapters | null | undefined) | undefined` (experimental) — Hook the \`RemoteThreadList\` store entry calls once per mounted thread body (the main-thread slot by default; every started thread with \`backgroundThreads\`), then provides to the \`thread\` factory. This is not mounted per listed thread. \`useRemoteThreadListRuntime\` also calls it except while it renders \`unstable_Provider\`. Resolve \`threadListItem\` lazily on each adapter call; do not capture it at hook mount. The hook must keep a stable hook count across adapter swaps; a different count throws. Memoize the returned object. Per-thread history also requires the \`thread\` factory to be keyed by thread id (\`withKey(id, thread(...))\`). History loaders such as \`useExternalHistory\` run once per mount; an unkeyed factory keeps one instance across switches and the next thread's messages never load.

  - `initialThreadId?`: `string | undefined` (deprecated: Use \`threadId\` instead, which also reacts to subsequent changes.) — When provided, the runtime starts on this thread instead of creating a new empty thread. Useful for URL-based routing (e.g. \`/chat/\[threadId]\`) where the initial thread is known at mount time.

  - `threadId?`: `string | undefined` — The current thread ID to display. When this value changes, the runtime automatically switches to the specified thread. Set to \`undefined\` to switch to a new thread.

  - `onThreadIdChange?`: `((threadId: string | undefined) => void) | undefined` — Called whenever the runtime changes the active thread's canonical (remote) ID, so the value can be treated as a managed/controlled variable (e.g. synced to a URL query param). Changes initiated by the controlled \`threadId\` option are not echoed back. Together these options form the controlled pattern: \`threadId\` in, \`onThreadIdChange\` out. Only the settled remote ID is emitted: while a freshly created thread is still optimistic (no remote ID yet) the value is \`undefined\`, and the real ID is emitted once the thread is initialized. The transient local ID is never surfaced.

  - `allowNesting?`: `boolean | undefined` — When true, if this runtime is used inside another RemoteThreadListRuntime, it becomes a no-op and simply calls the runtimeHook directly. This allows wrapping runtimes that internally use RemoteThreadListRuntime.