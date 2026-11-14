# assistant-cloud
URL: /docs/api-reference/integrations/assistant-cloud

The assistant-cloud client for threads, messages, runs, events, scores and files, with the reporters, persistence and run report helpers an integration is built from.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

`assistant-cloud` is the client the assistant-ui runtimes drive and the building blocks an integration of your own is made of. It runs in the browser, in React Native, in Node and on the edge, and its only dependency is `assistant-stream`. `@assistant-ui/react` re-exports `AssistantCloud` and `readAnonymousRefreshToken`. The guides are under [Assistant Cloud](/docs/cloud); the wire protocol is the [REST API](/docs/cloud/api).

## The client

`new AssistantCloud(config)` takes one of the three [authentication modes](/docs/cloud/authorization), `{ baseUrl, anonymous: true }`, `{ baseUrl, authToken }` or, on a server only, `{ apiKey, userId, workspaceId }`, and an optional `telemetry` configuration: `enabled`, `events`, `environment`, `release`, `tags` and `beforeReport`, with `telemetry: false` as shorthand for off. Its members are one sub client per resource:

| Member                           | Calls                                                                                                                                                                                                                              |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `threads`                        | `list({ is_archived?, limit?, after? })`, `get(id)`, `create({ last_message_at, title?, metadata?, external_id? })`, `update(id, { title?, last_message_at?, metadata?, is_archived? })`, `delete(id)`, `claim({ refresh_token })` |
| `threads.messages`               | `list(threadId, { format?, limit?, after? })`, `create(threadId, { parent_id, format, content })`, `update(threadId, messageId, { content })`, `delete(threadId, messageIds)`, `feedback(threadId, messageId, { type })`           |
| `runs`                           | `report(report)`, `stream({ thread_id, assistant_id, messages })`                                                                                                                                                                  |
| `events`                         | `track({ kind, thread_id?, message_id?, run_id?, value?, props? })`; events are batched and flushed after two seconds of quiet, at twenty waiting and when the page hides                                                          |
| `scores`                         | `create({ name, data_type, value?, string_value?, comment?, thread_id?, message_id?, run_id? })`                                                                                                                                   |
| `files`                          | `generatePresignedUploadUrl({ filename, content_type?, content_length? })`, `generatePresignedDownloadUrl({ key } \| { url })`                                                                                                     |
| `auth.tokens`                    | `create()`, a user token minted with an API key                                                                                                                                                                                    |
| `projects.threads`               | the project wide thread and message reads of an API key                                                                                                                                                                            |
| `telemetry`                      | the normalized telemetry configuration                                                                                                                                                                                             |
| `registerSdk({ name, version })` | adds an integration's identity to the `Aui-Sdk` header; the runtimes call it for you                                                                                                                                               |

Every call returns a promise and throws `CloudAPIError` on a non 2xx response, with `status`, `code` (the response's `error` field, for instance `plan_limit_reached`) and `details`, or `CloudResponseError` when a response does not have the expected shape.

## Building an integration

`CloudRunReporter` and `CloudEngagementReporter` are the two reporters every integration shares, `createRunReport` builds the wire body from a `RunReportInit`, and `createFormattedPersistence` over `CloudMessagePersistence` stores messages in a format of your choice. [`assistant-cloud/ai-sdk`](/docs/api-reference/integrations/assistant-cloud-ai-sdk) supplies the AI SDK format adapter and telemetry extraction, [`assistant-cloud/telemetry`](/docs/api-reference/integrations/assistant-cloud-telemetry) the OpenTelemetry exporter. The [package README](https://github.com/assistant-ui/assistant-ui/tree/main/packages/cloud#building-an-integration) walks through how they fit together.

## API Reference

### AssistantCloud

- `constructor?`: `(config: AssistantCloudConfig) => AssistantCloud`
- `threads?`: `AssistantCloudThreads`
- `projects?`: `AssistantCloudProjects`
- `auth?`: `{ tokens: AssistantCloudAuthTokens; invalidate: () => void; }`
- `runs?`: `AssistantCloudRuns`
- `files?`: `AssistantCloudFiles`
- `events?`: `AssistantCloudEvents`
- `scores?`: `AssistantCloudScores`
- `telemetry?`: `AssistantCloudTelemetryConfig`
- `registerSdk?`: `(sdk: SdkIdentity) => void`

### AssistantCloudEvents

- `constructor?`: `(cloud: AssistantCloudAPI, isEnabled: () => boolean) => AssistantCloudEvents`
- `track?`: `(event: AssistantCloudEvent) => void`
- `dispose?`: `() => void`

### AssistantCloudScores

- `constructor?`: `(cloud: AssistantCloudAPI) => AssistantCloudScores`
- `create?`: `(body: AssistantCloudScoreBody) => Promise<AssistantCloudScoreResponse>`

### AssistantCloudThreadRuns

Run leases on thread messages. A lease is opened with `open_run` when its message is created, belongs to that message alone, and runs until its holder ends it; the server never expires one. The holder heartbeats on the server's `heartbeat_interval_ms`, and listed messages report how long ago the last beat was, so readers can tell a run whose tab went away. Writes as the holder go through `threads.messages` with the `lease_id`.

- `constructor?`: `(cloud: AssistantCloudAPI) => AssistantCloudThreadRuns`
- `heartbeat?`: `(threadId: string, leaseId: string) => Promise<AssistantCloudRunHeartbeatResponse>` — Records a beat of a running lease. 409 \`run_ended\` after it ended, 404 for a lease the server does not know.
- `end?`: `(threadId: string, leaseId: string, body: { reason?: CloudRunEndReason } = {}) => Promise<void>` — Ends a run without a content write; ending an ended run again succeeds and keeps the first reason. 404 for a lease the server does not know.

### CloudAPIError

- `constructor?`: `(message: string, status: number, code?: string, details?: Record<string, unknown>) => CloudAPIError`
- `status?`: `number`
- `code?`: `string`
- `details?`: `Record<string, unknown>`

### CloudEngagementReporter

Derives engagement events from what a chat integration observes and keeps the per thread state the events need: a run's start for the stop duration, a run's end for the time to the next message, one error and one suggestion list per run or thread. A started run is kept until it ends or stops; the rest is kept for the 256 most recently touched threads, so a long session does not grow it without bound. Delivery goes through the cloud's event buffer, so a disabled telemetry setting drops everything here as well.

- `constructor?`: `(cloud: AssistantCloud | (() => AssistantCloud), resolveIds: EngagementIdResolver = passThroughIds) => CloudEngagementReporter`
- `runStarted?`: `(threadId: string) => void`
- `runEnded?`: `(threadId: string) => void`
- `runStopped?`: `(threadId: string) => void` — Reported once per started run, with the time the run had been going.
- `messageSent?`: `(threadId: string, init: { messageId?: string | undefined; chars: number; attachments: number; }) => void` — Carries the time since the previous run of the thread ended, when known.
- `messageEdited?`: `(threadId: string, init: { messageId: string; chars: number }) => void`
- `messageRegenerated?`: `(threadId: string, messageId?: string) => void`
- `errorShown?`: `(threadId: string, init: { messageId?: string | undefined; reason: string }) => void` — Reported once per run, so a retried render of the same error stays one event.
- `suggestionsShown?`: `(threadId: string, count: number) => void` — Reported once per thread, with the number of suggestions on offer.
- `suggestionClicked?`: `(threadId: string) => void`
- `attachmentAdded?`: `(threadId: string, init: { messageId?: string | undefined; contentType?: string | undefined }) => void`
- `attachmentFailed?`: `(threadId: string, init: { messageId?: string | undefined; contentType?: string | undefined }) => void`
- `voiceStarted?`: `(threadId: string) => void`
- `speechStarted?`: `(threadId: string, messageId?: string) => void`
- `branchSwitched?`: `(threadId: string, messageId?: string) => void`
- `messageCopied?`: `(threadId: string, messageId?: string) => void`
- `toolApproved?`: `(threadId: string, messageId: string, toolCallId: string, toolName: string) => void`
- `toolRejected?`: `(threadId: string, messageId: string, toolCallId: string, toolName: string) => void`
- `threadSwitched?`: `(threadId: string) => void` — Reported only for a thread the cloud already knows.

### CloudMessagePersistence

Appends, updates and loads cloud messages while mapping local ids to cloud ids and chaining parent_id. A parent that is still being created is awaited, so concurrent appends land under the right parent. A parent whose create failed is created again before its child, and a child whose parent still cannot be created is stored as a root.

A message appended as running holds a run lease, renewed by heartbeats on the server's interval until a write with an end reason ends it. Heartbeats keep running while the page is in the background. The server never expires a lease; readers show a run as stale once it has gone `staleAfter` without a beat, and a late run keeps writing its own message. A lease that is lost (ended elsewhere or refused) is final: the message gets no further writes from this persistence.

- `constructor?`: `(cloud: AssistantCloud | (() => AssistantCloud), options: CloudMessagePersistenceOptions = {}) => CloudMessagePersistence`
- `runStateOf?`: `(run: CloudMessageRun | undefined, sinceReadMs: number = 0) => CloudRunState | undefined` — Reads a listed message's run against \`staleAfter\`; \`sinceReadMs\` is the time since the list was read. Undefined for no run or an ended one.
- `append?`: `(threadId: string, messageId: string, parentId: string | null, format: string, content: ReadonlyJSONObject, run?: CloudMessageRunPhase) => Promise<void>` — Persist a message to the cloud.
- `update?`: `(threadId: string, messageId: string, _format: string, content: ReadonlyJSONObject, run?: CloudMessageRunPhase) => Promise<void>` — Update an already-persisted message in the cloud.
- `rename?`: `(from: string, to: string) => Promise<void>` — Moves a stored message's id mapping and run lease to a new local id, for runtimes that swap a placeholder's id once the backend names the message.
- `hasOpenRun?`: `(messageId: string) => boolean` — Whether a run lease this persistence opened for the message is still held.
- `delete?`: `(threadId: string, messageIds: string[]) => Promise<void>` — Delete mapped messages from the cloud and clear their local ID mappings.
- `isPersisted?`: `(messageId: string) => boolean` — Check if a message has been persisted (or is currently being persisted).
- `getRemoteId?`: `(messageId: string) => Promise<string | undefined>` — Get the remote ID for a local message ID (resolved). Returns undefined if not persisted.
- `getResolvedRemoteId?`: `(messageId: string) => string | undefined`
- `record?`: `(localId: string, remoteId: string) => void`
- `load?`: `(threadId: string, format?: string) => Promise<CloudMessage[]>` — Load messages from the cloud and populate the ID mapping. The list endpoint caps a response at 200 rows, so pages are followed by message ID cursor until a short page and concatenated in server order. The ID mapping is populated so that \`isPersisted()\` returns true for loaded messages, preventing re-persistence of already-stored messages. A loaded ID that an append already maps keeps the remote ID from that append, and falls back to the loaded ID if the append fails.
- `reset?`: `() => void` — Reset the ID mapping (call when switching threads). Pending \`load()\` and \`append()\` calls are not cancelled and still settle normally, but their results no longer populate the ID mapping.

### CloudResponseError

- `constructor?`: `(message: string) => CloudResponseError`

### CloudRunReporter

Sends run reports the way every client integration has to: nothing while telemetry is off, the cloud's environment, release and tags on every report, the `beforeReport` hook applied last, and a failed send that never surfaces. A keyed report is deduplicated while in flight and after an attempt. A rate-limited attempt releases the key so a later observation can try again.

- `constructor?`: `(cloud: AssistantCloud | (() => AssistantCloud)) => CloudRunReporter`
- `report?`: `(init: CloudRunReportInit, key?: string) => Promise<void>`

### createFormattedPersistence

Wraps a CloudMessagePersistence with a MessageFormatAdapter's encode and decode. The persistence parameter is typed structurally, so a caller does not need to import the class.

`append` and `update` take an optional run phase that is passed through to the persistence: `running` opens (on append) or renews (on update) the message's run lease, and an end reason makes the write the lease's final write and ends it, the same as for `aui/v0` messages.

```
const createFormattedPersistence: <TMessage, TStorageFormat>(persistence: { append: (threadId: string, messageId: string, parentId: string | null, format: string, content: ReadonlyJSONObject, run?: CloudMessageRunPhase) => Promise<void>; load: (threadId: string, format?: string) => Promise<any[]>; isPersisted: (messageId: string) => boolean; update?: (threadId: string, messageId: string, format: string, content: ReadonlyJSONObject, run?: CloudMessageRunPhase) => Promise<void>; delete?: (threadId: string, messageIds: string[]) => Promise<void>; }, adapter: MessageFormatAdapter<TMessage, TStorageFormat>) => { append: (threadId: string, item: { parentId: string | null; message: TMessage; }, run?: CloudMessageRunPhase) => Promise<void>; update: ((threadId: string, item: { parentId: string | null; message: TMessage; }, messageId: string, run?: CloudMessageRunPhase) => Promise<void>) | undefined; delete: ((threadId: string, items: { parentId: string | null; message: TMessage; }[]) => Promise<void>) | undefined; load: (threadId: string) => Promise<{ messages: { parentId: string | null; message: TMessage; }[]; }>; isPersisted: (messageId: string) => boolean; };
```

### createRunReport

- `init`: `RunReportInit`

  - `threadId`: `string`

  - `status`: `AssistantCloudRunReport["status"]`

  - `outcome?`: `AssistantCloudRunReport["outcome_type"] | undefined`

  - `errorCode?`: `string | undefined`

  - `error?`: `string | undefined`

  - `messageId?`: `string | undefined`

  - `userMessageId?`: `string | undefined`

  - `traceId?`: `string | undefined`

  - `rootSpanId?`: `string | undefined`

  - `modelId?`: `string | undefined`

  - `provider?`: `string | undefined`

  - `usage?`: `RunTelemetryUsageInit | undefined`

    - `inputTokens?`: `number | undefined`
    - `outputTokens?`: `number | undefined`
    - `reasoningTokens?`: `number | undefined`
    - `cachedInputTokens?`: `number | undefined`
    - `promptTokens?`: `number | undefined`
    - `completionTokens?`: `number | undefined`
    - `inputTokenDetails?`: `RunReportInit["usage"]["inputTokenDetails"]`
      - `cacheReadTokens?`: `number`
    - `outputTokenDetails?`: `RunReportInit["usage"]["outputTokenDetails"]`
      - `reasoningTokens?`: `number`

  - `steps?`: `RunReportStepInit[] | undefined`

  - `totalSteps?`: `number | undefined`

  - `toolCalls?`: `AssistantCloudRunReportToolCall[] | undefined`

  - `durationMs?`: `number | undefined`

  - `firstTokenMs?`: `number | undefined`

  - `costUsd?`: `number | undefined`

  - `costDetails?`: `RunReportInit["costDetails"]`

    - `input?`: `number | undefined`
    - `inputCachedTokens?`: `number | undefined`
    - `output?`: `number | undefined`
    - `total?`: `number | undefined`

  - `outputText?`: `string | undefined`

  - `attributes?`: `Record<string, unknown> | undefined`

  - `metadata?`: `Record<string, unknown> | undefined`

  - `telemetry?`: `RunReportInit["telemetry"]`

    - `environment?`: `string | undefined`
    - `release?`: `string | undefined`
    - `tags?`: `readonly string[] | undefined`

### createRunTelemetryToolCall

Serializes one tool call into the shape the runs endpoint accepts. An `mcp` source has its result summarized, because MCP content blocks carry inline base64 image and audio payloads that would otherwise dominate the report.

- `init`: `RunTelemetryToolCallInit`

  - `toolName`: `string`
  - `toolCallId`: `string`
  - `args?`: `unknown`
  - `argsText?`: `string | undefined` — Pre-serialized arguments, used in place of serializing \`args\`. Values over the span size are clamped before they are included in the report.
  - `result?`: `unknown`
  - `toolSource?`: `"mcp" | "frontend" | "backend" | undefined`

### createSamplingCollector

Creates a collector that accumulates sampling call data during tool execution. Use with `wrapSamplingHandler` to capture all sampling calls for a tool invocation.

```
const collector = createSamplingCollector();
const wrappedHandler = wrapSamplingHandler(handler, collector.collect);
// ... execute MCP tool ...
const calls = collector.getCalls(); // SamplingCallData[]
```

```
function createSamplingCollector(): { collect: (data: SamplingCallData) => number; getCalls: () => SamplingCallData[]; reset: () => void; };
```

### DEFAULT_RUN_STALE_AFTER_MS

How long a running lease may go without a heartbeat before its message reads as stale.

```
const DEFAULT_RUN_STALE_AFTER_MS: 30000;
```

### deriveRunOutcome

Maps a finish event to the report status and outcome. `fallbackStatus` applies when the event carries neither a finish reason nor a failure flag.

- `input`: `{ finishReason?: string | undefined; isAbort?: boolean | undefined; isDisconnect?: boolean | undefined; isError?: boolean | undefined; }`

  - `finishReason?`: `string | undefined`
  - `isAbort?`: `boolean | undefined`
  - `isDisconnect?`: `boolean | undefined`
  - `isError?`: `boolean | undefined`

- `fallbackStatus?`: `"incomplete" | "completed"`

### describeRunError

Reads the message, code and outcome the runs endpoint stores for a failed run. The code is the error's HTTP `statusCode` when it carries one, else its `code`, else its class name. The outcome comes from the HTTP status.

- `error`: `unknown`

### extractRunTelemetryModelId

Resolves the model ID a run reports, in the order an app can supply it: an explicit `modelId`, the `custom` bag, then the per-step `response.modelId` that a `messageMetadata` callback copies off the AI SDK's finish-step part. The AI SDK puts no model ID on a UI message part, so message metadata is the only channel one arrives on.

- `metadata`: `Record<string, unknown>`

### generateThreadTitle

- `cloud`: `AssistantCloud`

  - `threads`: `AssistantCloudThreads`

    - `messages`: `AssistantCloudThreadMessages`

      - `cloud`: `AssistantCloudAPI`
      - `list`: `(threadId: string, query?: AssistantCloudThreadMessageListQuery) => Promise<AssistantCloudThreadMessageListResponse>`
      - `create`: `(threadId: string, body: AssistantCloudThreadMessageCreateBody) => Promise<AssistantCloudMessageCreateResponse>`
      - `update`: `(threadId: string, messageId: string, body: AssistantCloudThreadMessageUpdateBody) => Promise<void>`
      - `delete`: `(threadId: string, messageIds: string[]) => Promise<void>`
      - `feedback`: `(threadId: string, messageId: string, body: AssistantCloudThreadMessageFeedbackBody) => Promise<AssistantCloudThreadMessageFeedbackResponse>`

    - `runs`: `AssistantCloudThreadRuns`

      - `cloud`: `AssistantCloudAPI`
      - `heartbeat`: `(threadId: string, leaseId: string) => Promise<AssistantCloudRunHeartbeatResponse>` — Records a beat of a running lease. 409 \`run_ended\` after it ended, 404 for a lease the server does not know.
      - `end`: `(threadId: string, leaseId: string, body?: { reason?: CloudRunEndReason; }) => Promise<void>` — Ends a run without a content write; ending an ended run again succeeds and keeps the first reason. 404 for a lease the server does not know.

    - `cloud`: `AssistantCloudAPI`

      - `_auth`: `AssistantCloudAuthStrategy`
      - `_baseUrl`: `string`
      - `registerSdk`: `(sdk: SdkIdentity) => void`
      - `sdkHeader`: `() => string`
      - `initializeAuth`: `() => Promise<boolean>`
      - `invalidateAuth`: `() => void`
      - `makeRawRequest`: `(endpoint: string, options?: MakeRequestOptions) => Promise<Response>`
      - `makeRequest`: `(endpoint: string, options?: MakeRequestOptions) => Promise<any>`

    - `list`: `(query?: AssistantCloudThreadsListQuery) => Promise<AssistantCloudThreadsListResponse>`

    - `get`: `(threadId: string) => Promise<CloudThread>`

    - `create`: `(body: AssistantCloudThreadsCreateBody) => Promise<AssistantCloudThreadsCreateResponse>`

    - `update`: `(threadId: string, body: AssistantCloudThreadsUpdateBody) => Promise<void>`

    - `claim`: `(body: AssistantCloudThreadsClaimBody) => Promise<AssistantCloudThreadsClaimResponse>` — Moves every thread of the anonymous identity behind \`refresh_token\` into the caller's workspace.

    - `delete`: `(threadId: string) => Promise<void>`

  - `projects`: `AssistantCloudProjects`

    - `threads`: `AssistantCloudProjectThreads`

      - `messages`: `AssistantCloudProjectThreadMessages`
      - `cloud`: `AssistantCloudAPI`
      - `list`: `(query?: AssistantCloudProjectThreadsListQuery) => Promise<AssistantCloudProjectThreadsListResponse>`

  - `auth`: `__object`

    - `tokens`: `AssistantCloudAuthTokens`

      - `cloud`: `AssistantCloudAPI`
      - `create`: `() => Promise<AssistantCloudAuthTokensCreateResponse>`

    - `invalidate`: `() => void`

  - `runs`: `AssistantCloudRuns`

    - `cloud`: `AssistantCloudAPI`

      - `_auth`: `AssistantCloudAuthStrategy`
      - `_baseUrl`: `string`
      - `registerSdk`: `(sdk: SdkIdentity) => void`
      - `sdkHeader`: `() => string`
      - `initializeAuth`: `() => Promise<boolean>`
      - `invalidateAuth`: `() => void`
      - `makeRawRequest`: `(endpoint: string, options?: MakeRequestOptions) => Promise<Response>`
      - `makeRequest`: `(endpoint: string, options?: MakeRequestOptions) => Promise<any>`

    - `stream`: `(body: AssistantCloudRunsStreamBody) => Promise<AssistantStream>`

    - `report`: `(body: AssistantCloudRunReport) => Promise<{ run_id: string; }>`

  - `files`: `AssistantCloudFiles`

    - `cloud`: `AssistantCloudAPI`

      - `_auth`: `AssistantCloudAuthStrategy`
      - `_baseUrl`: `string`
      - `registerSdk`: `(sdk: SdkIdentity) => void`
      - `sdkHeader`: `() => string`
      - `initializeAuth`: `() => Promise<boolean>`
      - `invalidateAuth`: `() => void`
      - `makeRawRequest`: `(endpoint: string, options?: MakeRequestOptions) => Promise<Response>`
      - `makeRequest`: `(endpoint: string, options?: MakeRequestOptions) => Promise<any>`

    - `pdfToImages`: `(body: PdfToImagesRequestBody) => Promise<PdfToImagesResponse>` (deprecated: Assistant Cloud has no PDF conversion endpoint, so this request always rejects with a \`CloudAPIError\` whose \`status\` is 404.)

    - `generatePresignedUploadUrl`: `(body: GeneratePresignedUploadUrlRequestBody) => Promise<GeneratePresignedUploadUrlResponse>`

    - `generatePresignedDownloadUrl`: `(body: { key: string; } | { url: string; }) => Promise<GeneratePresignedDownloadUrlResponse>`

  - `events`: `AssistantCloudEvents`

    - `buffer`: `AssistantCloudEvent[]`

    - `timer?`: `ReturnType<typeof setTimeout> | undefined`

      - `close`: `() => Timeout` — Cancels the timeout.
      - `hasRef`: `() => boolean` — If true, the \`Timeout\` object will keep the Node.js event loop active.
      - `ref`: `() => Timeout` — When called, requests that the Node.js event loop \_not\_ exit so long as the \`Timeout\` is active. Calling \`timeout.ref()\` multiple times will have no effect. By default, all \`Timeout\` objects are "ref'ed", making it normally unnecessary to call \`timeout.ref()\` unless \`timeout.unref()\` had been called previously.
      - `refresh`: `() => Timeout` — Sets the timer's start time to the current time, and reschedules the timer to call its callback at the previously specified duration adjusted to the current time. This is useful for refreshing a timer without allocating a new JavaScript object. Using this on a timer that has already called its callback will reactivate the timer.
      - `unref`: `() => Timeout` — When called, the active \`Timeout\` object will not require the Node.js event loop to remain active. If there is no other activity keeping the event loop running, the process may exit before the \`Timeout\` object's callback is invoked. Calling \`timeout.unref()\` multiple times will have no effect.
      - `_onTimeout`: `(...args: any[]) => void`

    - `flushing?`: `Promise<void> | undefined`

      - `then`: `<TResult1, TResult2>(onfulfilled?: ((value: void) => TResult1 | PromiseLike<TResult1>) | null | undefined, onrejected?: ((reason: any) => TResult2 | PromiseLike<TResult2>) | null | undefined) => Promise<TResult1 | TResult2>`
      - `catch`: `<TResult>(onrejected?: ((reason: any) => TResult | PromiseLike<TResult>) | null | undefined) => Promise<void | TResult>`
      - `finally`: `(onfinally?: (() => void) | null | undefined) => Promise<void>`

    - `retryTimer?`: `ReturnType<typeof setTimeout> | undefined`

      - `close`: `() => Timeout` — Cancels the timeout.
      - `hasRef`: `() => boolean` — If true, the \`Timeout\` object will keep the Node.js event loop active.
      - `ref`: `() => Timeout` — When called, requests that the Node.js event loop \_not\_ exit so long as the \`Timeout\` is active. Calling \`timeout.ref()\` multiple times will have no effect. By default, all \`Timeout\` objects are "ref'ed", making it normally unnecessary to call \`timeout.ref()\` unless \`timeout.unref()\` had been called previously.
      - `refresh`: `() => Timeout` — Sets the timer's start time to the current time, and reschedules the timer to call its callback at the previously specified duration adjusted to the current time. This is useful for refreshing a timer without allocating a new JavaScript object. Using this on a timer that has already called its callback will reactivate the timer.
      - `unref`: `() => Timeout` — When called, the active \`Timeout\` object will not require the Node.js event loop to remain active. If there is no other activity keeping the event loop running, the process may exit before the \`Timeout\` object's callback is invoked. Calling \`timeout.unref()\` multiple times will have no effect.
      - `_onTimeout`: `(...args: any[]) => void`

    - `resolveRetryDelay?`: `(() => void) | undefined`

    - `bestEffortRequested`: `boolean`

    - `generation`: `number`

    - `cloud`: `AssistantCloudAPI`

      - `_auth`: `AssistantCloudAuthStrategy`
      - `_baseUrl`: `string`
      - `registerSdk`: `(sdk: SdkIdentity) => void`
      - `sdkHeader`: `() => string`
      - `initializeAuth`: `() => Promise<boolean>`
      - `invalidateAuth`: `() => void`
      - `makeRawRequest`: `(endpoint: string, options?: MakeRequestOptions) => Promise<Response>`
      - `makeRequest`: `(endpoint: string, options?: MakeRequestOptions) => Promise<any>`

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

      - `_auth`: `AssistantCloudAuthStrategy`
      - `_baseUrl`: `string`
      - `registerSdk`: `(sdk: SdkIdentity) => void`
      - `sdkHeader`: `() => string`
      - `initializeAuth`: `() => Promise<boolean>`
      - `invalidateAuth`: `() => void`
      - `makeRawRequest`: `(endpoint: string, options?: MakeRequestOptions) => Promise<Response>`
      - `makeRequest`: `(endpoint: string, options?: MakeRequestOptions) => Promise<any>`

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

- `options`: `{ threadId: string; messages: readonly { role: string; content: readonly { type: "text"; text: string; }[]; }[]; }`

  - `threadId`: `string`
  - `messages`: `readonly { role: string; content: readonly { type: "text"; text: string }[]; }[]`

### normalizeRunTelemetryUsage

Resolves the token counts a provider reports under any of the names the AI SDK has used: the current top-level ones, the legacy prompt/completion pair, and the v7 token detail objects. Returns undefined when no count is present, so callers can tell an empty usage object from a zeroed one.

- `usage`: `RunTelemetryUsageInit`

  - `inputTokens?`: `number | undefined`
  - `outputTokens?`: `number | undefined`
  - `reasoningTokens?`: `number | undefined`
  - `cachedInputTokens?`: `number | undefined`
  - `promptTokens?`: `number | undefined`
  - `completionTokens?`: `number | undefined`
  - `inputTokenDetails?`: `RunTelemetryUsageInit["inputTokenDetails"]`
    - `cacheReadTokens?`: `number`
  - `outputTokenDetails?`: `RunTelemetryUsageInit["outputTokenDetails"]`
    - `reasoningTokens?`: `number`

### readAnonymousRefreshToken

The refresh token of the anonymous identity this browser holds for `baseUrl`, or null when it has none.

```
const readAnonymousRefreshToken: (baseUrl: string) => string | null;
```

### truncateRunTelemetryText

Clamps a string to the size the runs endpoint accepts for a single span field.

- `value`: `string`

### wrapSamplingHandler

Wraps an MCP sampling handler to intercept and measure sampling calls.

```
const samplingCalls: SamplingCallData[] = [];
const wrapped = wrapSamplingHandler(
  originalHandler,
  (data) => samplingCalls.push(data),
);
// Use `wrapped` as the MCP client's sampling handler
// After tool execution, `samplingCalls` contains metrics for all nested LLM calls
```

- `handler`: `McpSamplingHandler`
- `onSamplingCall`: `(data: SamplingCallData) => void`