# @assistant-ui/eve
URL: /docs/api-reference/integrations/eve

Eve runtime hook and message conversion utilities for assistant-ui React applications.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### convertEveMessage

Converts a single Eve message into an assistant-ui thread message.

```
const convertEveMessage: (message: EveMessage, index: number, messages: readonly EveMessage[], options?: ConvertEveMessagesOptions) => ThreadMessage;
```

### convertEveMessages

Converts Eve's conversation messages into assistant-ui thread messages.

```
const convertEveMessages: (data: Pick<ConversationState, "messages">, options?: ConvertEveMessagesOptions) => ThreadMessage[];
```

### getEveMessageContent

Converts an assistant-ui append message into the message payload accepted by Eve's `send` API.

```
const getEveMessageContent: (message: AppendMessage) => EveMessageContent;
```

### toEveInputResponse

Converts an assistant-ui tool approval response into an Eve `Answer`.

When `inputRequest` is known, every returned answer carries an option the request declares, the literal `"approve"` / `"deny"` for an approval that declares no options, or a free-form answer. A literal option match wins, then the `"approve"` / `"deny"` option the response's boolean decision names, then the response's `text` (or its `reason`, for a caller that predates the first-class answer field) when the request takes a free-form answer (`display: "text"`, `allowFreeform`, or no options at all, and never `display: "confirmation"`) and the response is not a refusal.

A response the known request cannot record as the answer it asked for throws instead of being submitted, because Eve resolves an interaction the moment any answer for it arrives, and an empty one is recorded as an answer with no content. Not sending leaves the interaction open, so the caller can retry or the user can answer it as an ordinary message.

With no request to map against, a one-argument call forwards a caller `optionId` as is and otherwise maps the decision to the literal `"approve"` / `"deny"` option every Eve approval declares.

```
const toEveInputResponse: (response: RespondToToolApprovalOptions, inputRequest?: Interaction["request"]) => Answer;
```

### useEveAgentRuntime

Connects Eve's `useEveAgent` hook to assistant-ui's runtime contract.

The runtime renders Eve messages, forwards new user messages to the Eve session, supports cancellation, and maps Eve interactions to assistant-ui tool approval UI.

- `options?`: `UseEveAgentRuntimeOptions`

  - `agent?`: `string` — Named agent mounted by a framework integration such as \`withEve({ agents })\`. \`agent: "support"\` targets same-origin routes under \`/eve/support/v1/...\`. Do not combine with \`baseUrl\`.

  - `auth?`: `ClientAuth`

    - `basic`: `ClientAuth["basic"]`

      - `username`: `string`
      - `password`: `TokenValue`

    - `bearer`: `TokenValue`

    - `vercelOidc`: `ClientAuth["vercelOidc"]`
      - `token`: `TokenValue`

  - `headers?`: `HeadersValue`

  - `baseUrl`: `string` (default `""`) — Base URL for eve client requests. Do not combine with \`agent\`. Defaults to same-origin eve routes such as \`/eve/v1/...\`. Pass a same-origin prefix such as \`/api\` for an app-owned proxy, or an absolute origin to talk to an eve server directly.

  - `initialEvents?`: `readonly SessionStreamEvent[]` — Ordered prefix of the session stream used to rehydrate projected state.

  - `initialSession?`: `ClientSessionState`

    - `inbox?`: `string` — Opaque delivery routing hint; preserve it when saving and restoring a session.
    - `sessionId`: `string`
    - `streamIndex`: `number`

  - `optimistic`: `boolean` (default `true`) — Project submitted user messages before eve confirms them with a \`delivery.consumed\` fact. Such a message shows in \`messages\` only; \`events\` remains the authoritative eve stream.

  - `followAgents`: `SessionStoreInit["followAgents"]` (default `false`) — Recursively observe recorded children, or select relationships with a predicate, within store safety limits.

  - `followWhile`: `(view: SessionStoreSnapshot["view"]) => boolean` (default `() => true`) — Keep following while the committed view matches; local obligations always keep the stream open.

  - `prewarm`: `boolean` (default `false`) — Create a session or prepare its existing idle owner when true. React observes this value across renders; changing it from false to true prepares the current session, and \`newSession()\` checks the latest rendered value before preparing the next session. Changing the value to false does not discard an existing session or abort session creation already in flight.

  - `resume`: `boolean` (default `false`) — Replay the attached durable session after mount and follow its in-flight turn, if any. Requires \`initialSession\` or \`session\`.

  - `session?`: `ClientSession`

    - `#private`: `any`

    - `id`: `string` — The fixed session ID; reading it performs no I/O.

    - `state`: `ClientSessionState` — Current fixed session identity and durable stream cursor.

      - `inbox?`: `string` — Opaque delivery routing hint; preserve it when saving and restoring a session.
      - `sessionId`: `string`
      - `streamIndex`: `number`

    - `snapshot`: `(options?: { readonly signal?: AbortSignal; }) => Promise<SessionSnapshot>` — Reads a finite prefix through the durable tail without advancing this handle.

    - `send`: `<TOutput = unknown>(message: SendTurnInput<TOutput>["message"], options?: SendTurnOptions<TOutput>) => Promise<Delivery<TOutput>>` — Sends a message to this exact session ID.

    - `respond`: `<TOutput = unknown>(answers: readonly Answer[], options?: RespondTurnOptions<TOutput>) => Promise<Delivery<TOutput>>` — Answers pending input requests on this exact session ID.

    - `prewarm`: `(options?: { readonly signal?: AbortSignal; }) => Promise<PrewarmSessionResult>` — Requests preparation of an existing idle session; never starts a model turn.

    - `cancel`: `(options?: { readonly signal?: AbortSignal; readonly turnId?: string | null; }) => Promise<Delivery>` — Requests cooperative cancellation of this session's active turn.

    - `clear`: `(options?: { readonly signal?: AbortSignal; }) => Promise<Delivery>` — Queues removal of this session's durable model-message history.

    - `compact`: `(options?: { readonly signal?: AbortSignal; }) => Promise<Delivery>` — Queues context compaction without sending model input.

    - `reset`: `(options?: { readonly reason?: string; readonly signal?: AbortSignal; }) => Promise<Delivery>` — Terminally retires this exact session ID. The handle remains pinned to it.

    - `stream`: `(options?: SessionStreamOptions) => AsyncIterable<SessionStreamEvent>` — Opens this session's durable event stream from its stored cursor.

    - `agent`: `(opened: Extract<SessionStreamEvent, { type: "child.opened"; }>) => ClientAgentSession` — The session an agent run opened, as this session's stream linked it. Pass a \`child.opened\` event from this session, then follow the child with \`stream()\`; reads use this session's base URL and credentials, for local and remote agents alike.

  - `onError?`: `(error: Error) => void` — Reports live failures and transport errors; recorded historical failures only update \`error\`.

  - `onEvent?`: `(event: SessionStreamEvent) => void` — Observes newly read events, including catch-up events.

  - `onFinish?`: `(snapshot: SessionStoreSnapshot) => void` — Fires once per live settled turn after its complete commit, never for catch-up (even for a local receipt).

  - `onSessionChange?`: `(session: ClientSessionState | undefined) => void`

  - `prepareSend?`: `PrepareSend`

  - `suggestions?`: `readonly ThreadSuggestion[] | undefined`

  - `isDisabled?`: `boolean | undefined` — Whether the entire thread is disabled. When \`true\`, the composer input cannot be typed into, attachments cannot be added, sends (including direct \`composer.send()\` calls) are blocked, and messages cannot be edited. For a narrower gate that keeps the input usable but blocks only sending, use \`isSendDisabled\`.

  - `isSendDisabled?`: `boolean | undefined` — Whether sending new messages is currently disabled. When \`true\`, the thread composer's input remains usable but \`send()\` becomes a no-op and the thread composer's \`canSend\` is \`false\`. Use this to gate sending on external React state (e.g. while tool config is loading) without disabling the input itself the way \`isDisabled\` does. Edit composers (saving message edits) intentionally ignore this flag.

  - `unstable_capabilities?`: `UseEveAgentRuntimeOptions["unstable_capabilities"]` (experimental)
    - `copy?`: `boolean | undefined`

  - `adapters?`: `UseEveAgentRuntimeOptions["adapters"]`

    - `attachments?`: `AttachmentAdapter | undefined`

      - `accept`: `string`
      - `add`: `(state: { file: File; }) => Promise<PendingAttachment> | AsyncGenerator<PendingAttachment, void>` — Returns the pending attachment for the file, or an async generator yielding successive states of that one attachment. Every yield must have the same id.
      - `remove`: `(attachment: Attachment) => Promise<void>`
      - `send`: `(attachment: PendingAttachment, options?: { signal?: AbortSignal; }) => Promise<CompleteAttachment>`

    - `speech?`: `SpeechSynthesisAdapter | undefined`
      - `speak`: `(text: string) => SpeechSynthesisAdapter.Utterance`

    - `dictation?`: `DictationAdapter | undefined`

      - `listen`: `() => DictationAdapter.Session`
      - `disableInputDuringDictation?`: `boolean`

    - `voice?`: `RealtimeVoiceAdapter | undefined`
      - `connect`: `(options: { abortSignal?: AbortSignal; }) => RealtimeVoiceAdapter.Session`

    - `feedback?`: `FeedbackAdapter | undefined`
      - `submit`: `(feedback: FeedbackAdapterFeedback) => void`

  - `cloud?`: `AssistantCloud | undefined` — Backs the thread list with Assistant Cloud. Each cloud thread keeps one Eve session as its external id, saved once the thread's first turn creates the session, and opening a thread resumes it, so \`session\`, \`initialSession\`, \`initialEvents\`, and \`resume\` are not used. Pass it from the first render: adding or removing it later throws, so remount the runtime, for example with a \`key\`, to switch.

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

### useEveError

Read the last Eve session error from the runtime extras.

```
const useEveError: () => Error | undefined;
```

### useEveEvents

Read the authoritative Eve server event stream from the runtime extras. Defaults to an empty array outside an Eve runtime.

```
const useEveEvents: () => EveRuntimeExtras["events"];
```

### useEveReset

Returns a function that starts a new Eve session: it stops reading the current session and clears its events and messages, without cancelling a turn still running on the server. Pending staged messages and tool execution state are discarded. Safe to render outside an Eve runtime; invoking the returned function there throws.

With `cloud`, it starts a new thread instead, leaving the current thread and its session in the list.

```
const useEveReset: () => () => void;
```

### useEveSession

Read the current Eve session cursor from the runtime extras. Persist it to resume the session later via `initialSession`. `undefined` when no session exists yet or outside an Eve runtime.

With `cloud`, each cloud thread already keeps its session, so there is nothing to persist.

```
const useEveSession: () => EveRuntimeExtras["session"] | undefined;
```