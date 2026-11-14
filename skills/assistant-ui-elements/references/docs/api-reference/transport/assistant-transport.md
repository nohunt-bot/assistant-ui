# Assistant Transport
URL: /docs/api-reference/transport/assistant-transport

Command, protocol, and transport types for connecting assistant-ui runtimes across execution boundaries.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### AssistantTransportCommand

- `type`: `"add-message"`

### AssistantTransportConnectionMetadata

- `isSending`: `boolean`
- `toolStatuses`: `Record<string, ToolExecutionStatus>`
- `pendingCommands`: `AssistantTransportCommand[]`

### AssistantTransportProtocol

- `toString`: `() => string`
- `charAt`: `(pos: number) => string`
- `charCodeAt`: `(index: number) => number`
- `concat`: `(...strings: string[]) => string`
- `indexOf`: `(searchString: string, position?: number) => number`
- `lastIndexOf`: `(searchString: string, position?: number) => number`
- `localeCompare`: `{ (that: string): number; (that: string, locales?: string | string[], options?: Intl.CollatorOptions): number; (that: string, locales?: Intl.LocalesArgument, options?: Intl.CollatorOptions): number; }`
- `match`: `{ (regexp: string | RegExp): RegExpMatchArray | null; (matcher: { [Symbol.match](string: string): RegExpMatchArray | null; }): RegExpMatchArray | null; }`
- `replace`: `{ (searchValue: string | RegExp, replaceValue: string): string; (searchValue: string | RegExp, replacer: (substring: string, ...args: any[]) => string): string; (searchValue: { [Symbol.replace](string: string, replaceValue: string): string; }, replaceValue: string): string; (searchValue: { [Symbol.replace](string: string, replacer: (substring: string, ...args: any[]) => string): string; }, replacer: (substring: string, ...args: any[]) => string): string; }`
- `search`: `{ (regexp: string | RegExp): number; (searcher: { [Symbol.search](string: string): number; }): number; }`
- `slice`: `(start?: number, end?: number) => string`
- `split`: `{ (separator: string | RegExp, limit?: number): string[]; (splitter: { [Symbol.split](string: string, limit?: number): string[]; }, limit?: number): string[]; }`
- `substring`: `(start: number, end?: number) => string`
- `toLowerCase`: `() => string`
- `toLocaleLowerCase`: `{ (locales?: string | string[]): string; (locales?: Intl.LocalesArgument): string; }`
- `toUpperCase`: `() => string`
- `toLocaleUpperCase`: `{ (locales?: string | string[]): string; (locales?: Intl.LocalesArgument): string; }`
- `trim`: `() => string`
- `length`: `number`
- `substr`: `(from: number, length?: number) => string`
- `valueOf`: `() => string`
- `codePointAt`: `(pos: number) => number`
- `includes`: `(searchString: string, position?: number) => boolean`
- `endsWith`: `(searchString: string, endPosition?: number) => boolean`
- `normalize`: `{ (form: "NFC" | "NFD" | "NFKC" | "NFKD"): string; (form?: string): string; }`
- `repeat`: `(count: number) => string`
- `startsWith`: `(searchString: string, position?: number) => boolean`
- `anchor`: `(name: string) => string`
- `big`: `() => string`
- `blink`: `() => string`
- `bold`: `() => string`
- `fixed`: `() => string`
- `fontcolor`: `(color: string) => string`
- `fontsize`: `{ (size: number): string; (size: string): string; }`
- `italics`: `() => string`
- `link`: `(url: string) => string`
- `small`: `() => string`
- `strike`: `() => string`
- `sub`: `() => string`
- `sup`: `() => string`
- `padStart`: `(maxLength: number, fillString?: string) => string`
- `padEnd`: `(maxLength: number, fillString?: string) => string`
- `trimEnd`: `() => string`
- `trimStart`: `() => string`
- `trimLeft`: `() => string`
- `trimRight`: `() => string`
- `matchAll`: `(regexp: RegExp) => RegExpStringIterator<RegExpExecArray>`
- `replaceAll`: `{ (searchValue: string | RegExp, replaceValue: string): string; (searchValue: string | RegExp, replacer: (substring: string, ...args: any[]) => string): string; }`
- `at`: `(index: number) => string`

### SendCommandsRequestBody

- `state?`: `unknown` — Absent on a resume with \`resumeStateApi\`; the server replays from its retained snapshot.

- `runId?`: `string`

- `system?`: `string | undefined`

- `tools?`: `Record<string, unknown> | undefined`

- `callSettings?`: `LanguageModelV1CallSettings | undefined`

  - `maxTokens?`: `number`
  - `temperature?`: `number`
  - `topP?`: `number`
  - `presencePenalty?`: `number`
  - `frequencyPenalty?`: `number`
  - `seed?`: `number`
  - `headers?`: `Record<string, string | undefined>`

- `config?`: `LanguageModelConfig | undefined`

  - `apiKey?`: `string`
  - `baseUrl?`: `string`
  - `modelName?`: `string`
  - `reasoningEffort?`: `string`

- `threadId?`: `string` — Remote id of the thread; absent only when resuming a thread that has none.

- `parentId?`: `string | null`

- `commands`: `AssistantTransportCommand[]`

### useAssistantTransportRuntime

- `options`: `AssistantTransportOptions<T>`

  - `body?`: `object | (() => Promise<object | undefined>)`

  - `canResume?`: `boolean | undefined` — Whether the backend retains a checkpoint for the current thread. Requires resumeApi; clear when that checkpoint expires, finishes, or the thread changes.

  - `adapters?`: `AssistantTransportOptions["adapters"]`

    - `attachments?`: `AttachmentAdapter | undefined`

      - `accept`: `string`
      - `add`: `(state: { file: File; }) => Promise<PendingAttachment> | AsyncGenerator<PendingAttachment, void>` — Returns the pending attachment for the file, or an async generator yielding successive states of that one attachment. Every yield must have the same id.
      - `remove`: `(attachment: Attachment) => Promise<void>`
      - `send`: `(attachment: PendingAttachment, options?: { signal?: AbortSignal; }) => Promise<CompleteAttachment>`

    - `history?`: `ThreadHistoryAdapter | undefined` (deprecated: This runtime never reads it; pass \`cloud\` to keep threads in Assistant Cloud.)

      - `scopeId?`: `string | undefined` — Stable identity for the storage scope read by LocalRuntime. Keep it the same when recreating the adapter for one thread, account, or workspace, and change it before loading a different scope. Adapters that omit it are treated as sharing one scope. External-history runtimes do not read it.
      - `unstable_copy?`: `(( branch: readonly ThreadMessage[], messageIds: readonly string[], ) => Promise<void>) | undefined` (experimental) — Keeps a copy of messages whose source of truth is the runtime's backend. \`branch\` is the conversation from its first message to its last, and \`messageIds\` names the ones in it that are new or changed; the adapter stores those, and first any earlier message of the branch it does not hold yet, keyed by each message's own id. It is undefined while the adapter keeps no copies, so the runtime then neither copies nor records tool interactions.
      - `load`: `() => Promise<ExportedMessageRepository & { state?: ReadonlyJSONValue; unstable_resume?: boolean; }>`
      - `resume?`: `(options: ChatModelRunOptions) => AsyncGenerator<ChatModelRunResult, void, unknown>`
      - `append`: `(item: ExportedMessageRepositoryItem) => Promise<void>`
      - `update?`: `(item: ExportedMessageRepositoryItem) => Promise<void>` — Rewrites a previously appended message in place, keyed by its message id. Adapters that implement this let a runtime persist a run paused for tool approval, finalize the same message once the run resumes, and record a tool result that arrives after the message settled, which can be a message later turns follow. Without it, a paused run is appended when it ends or when a later turn follows its message while the run is still open; anything that run adds after the append is not stored. An update may arrive for an id whose earlier write failed; treat it as an upsert keyed on the message id rather than assuming the entry exists.
      - `delete?`: `(items: ExportedMessageRepositoryItem[]) => Promise<void>` — Deletes messages from history. The runtime may send a second delete for the same message after a write it issued before the delete lands; treat deleting a missing entry as success.
      - `withFormat?`: `<TMessage, TStorageFormat extends Record<string, unknown>>(formatAdapter: MessageFormatAdapter<TMessage, TStorageFormat>) => GenericThreadHistoryAdapter<TMessage>` — Required when used with \`useAISDKRuntime\` / \`useChatRuntime\`.

  - `capabilities?`: `AssistantTransportOptions["capabilities"]`
    - `edit?`: `boolean`

  - `headers`: `HeadersValue | (() => Promise<HeadersValue>)`

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

  - `initialState`: `T`

  - `api`: `string`

  - `cloud?`: `AssistantCloud | undefined` — Backs the thread list with Assistant Cloud; requests carry the cloud thread id. Without it, \`NEXT_PUBLIC_ASSISTANT_BASE_URL\` selects Assistant Cloud on Next.js, as for \`useLocalRuntime\`.

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

  - `resumeApi?`: `string`

  - `resumeStateApi?`: `string` — Endpoint that returns the retained initial state and run ID for a resume stream. A 204 response means no run is active and the resume is skipped.

  - `protocol?`: `AssistantTransportProtocol`

  - `strict?`: `boolean` — When \`false\`, stream decoding and state reconciliation tolerate malformed input (invalid chunks are dropped with a console log) instead of throwing. Resume runs always decode leniently. Defaults to \`true\`.

  - `maxStreamLineLength?`: `number | undefined` — Maximum UTF-16 code units accepted in one data-stream or SSE line. Defaults to 16 MiB.

  - `maxStreamEventLength?`: `number | undefined` — Maximum UTF-16 code units retained across one assistant-transport event. Defaults to 16 MiB.

  - `onResponse?`: `(response: Response) => void | Promise<void>`

  - `onFinish?`: `() => void`

  - `converter`: `AssistantTransportStateConverter<T>`

  - `prepareSendCommandsRequest?`: `( body: SendCommandsRequestBody, ) => Record<string, unknown> | Promise<Record<string, unknown>>`

  - `onError?`: `( error: Error, params: { commands: AssistantTransportCommand[]; updateState: (updater: (state: T) => T) => void; }, ) => void | Promise<void>`

  - `onCancel?`: `(params: { commands: AssistantTransportCommand[]; updateState: (updater: (state: T) => T) => void; error?: Error; }) => void`

### useAssistantTransportSendCommand

```
const useAssistantTransportSendCommand: () => (command: AssistantTransportCommand) => void;
```

### useAssistantTransportState

```
function useAssistantTransportState(): UserExternalState;
```