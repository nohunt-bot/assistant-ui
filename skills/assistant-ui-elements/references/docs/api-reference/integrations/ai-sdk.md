# @assistant-ui/ai-sdk
URL: /docs/api-reference/integrations/ai-sdk

Vercel AI SDK runtime hooks, chat transports, and message conversion utilities for assistant-ui applications.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### AISDKChat

`AuiConfig` entry that runs the AI SDK chat as the `threads` scope. Hosts the same orchestration as `useChatRuntime` inside the client's own resource tree, so it works with any `AssistantClient` host, React or not. Single thread; the multi-thread and assistant-cloud surface is [AISDKThreads](/docs/api-reference/integrations/ai-sdk#aisdkthreads). The chat id is captured when the entry mounts, so a later `id` change in the options has no effect.

- `0?`: `AISDKChatOptions<UI_MESSAGE>`

  - `id?`: `string` — A unique identifier for the chat. If not provided, a random one will be generated when first accessed.

  - `messageMetadataSchema?`: `FlexibleSchema$1<UI_MESSAGE['metadata']>`

  - `dataPartSchemas?`: `UIDataTypesToSchemas<InferUIMessageData<UI_MESSAGE>>`

  - `messages?`: `UI_MESSAGE[]`

  - `generateId?`: `IdGenerator$1` — A way to provide a function that is going to be used for ids for messages and the chat. If not provided the default AI SDK \`generateId\` is used.

  - `transport?`: `ChatTransport<UI_MESSAGE>`

    - `resumeStreamIsReplay?`: `boolean` — Whether reconnecting returns the complete response from its beginning. Complete replays must reset the streaming message state before processing the first start chunk.
    - `close?`: `() => void` — Releases persistent resources owned by the transport. The caller that creates a transport owns its lifecycle and should call this method when no consumers remain. \`AbstractChat.dispose()\` calls it for directly managed chat instances.
    - `sendMessages`: `(options: { /** The type of message submission - either new message or regeneration */ trigger: 'submit-message' | 'regenerate-message'; /** Unique identifier for the chat session */ chatId: string; /** ID of the message to regenerate, or undefined for new messages */ messageId: string | undefined; /** Array of UI messages representing the conversation history */ messages: UI_MESSAGE[]; /** Signal to abort the request if needed */ abortSignal: AbortSignal | undefined; } & ChatRequestOptions) => Promise<ReadableStream<UIMessageChunk>>` — Sends messages to the chat API endpoint and returns a streaming response. This method handles both new message submission and message regeneration. It supports real-time streaming of responses through UIMessageChunk events.
    - `reconnectToStream`: `(options: { /** Unique identifier for the chat session to reconnect to */ chatId: string; /** Signal to abort the reconnection request if needed */ abortSignal?: AbortSignal; } & ChatRequestOptions) => Promise<ReadableStream<UIMessageChunk> | null>` — Reconnects to an existing streaming response for the specified chat session. This method is used to resume streaming when a connection is interrupted or when resuming a chat session. It's particularly useful for maintaining continuity in long-running conversations or recovering from network issues.

  - `onError?`: `ChatOnErrorCallback` — Callback function to be called when an error is encountered.

  - `onToolCall?`: `ChatOnToolCallCallback<UI_MESSAGE>` — Optional callback function that is invoked when a tool call is received. Intended for automatic client-side tool execution. To add the tool output, call \`addToolOutput\` without awaiting it inside this callback. The callback's return value is not used.

  - `onFinish?`: `ChatOnFinishCallback<UI_MESSAGE>` — Function that is called when the assistant response has finished streaming.

  - `onData?`: `ChatOnDataCallback<UI_MESSAGE>` — Optional callback function that is called when a data part is received.

  - `sendAutomaticallyWhen?`: `(options: { messages: UI_MESSAGE[]; }) => boolean | PromiseLike<boolean>` — When provided, this function will be called when the stream is finished or a tool call is added to determine if the current messages should be resubmitted.

  - `suggestions?`: `readonly ThreadSuggestion[] | undefined`

  - `isDisabled?`: `boolean | undefined` — Whether the entire thread is disabled. When \`true\`, the composer input cannot be typed into, attachments cannot be added, sends (including direct \`composer.send()\` calls) are blocked, and messages cannot be edited. For a narrower gate that keeps the input usable but blocks only sending, use \`isSendDisabled\`.

  - `isSendDisabled?`: `boolean | undefined` — Whether sending new messages is currently disabled. When \`true\`, the thread composer's input remains usable but \`send()\` becomes a no-op and the thread composer's \`canSend\` is \`false\`. Use this to gate sending on external React state (e.g. while tool config is loading) without disabling the input itself the way \`isDisabled\` does. Edit composers (saving message edits) intentionally ignore this flag.

  - `unstable_capabilities?`: `AISDKChat props["0"]["unstable_capabilities"]` (experimental)
    - `copy?`: `boolean | undefined`

  - `throttle?`: `number | undefined`

  - `adapters?`: `AISDKRuntimeAdapter["adapters"] | undefined`

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

    - `threadList?`: `ExternalStoreThreadListAdapter | undefined` (experimental)

      - `threadId?`: `string | undefined` (experimental)
      - `isLoading?`: `boolean | undefined`
      - `threads?`: `readonly ExternalStoreThreadData<"regular">[] | undefined`
      - `archivedThreads?`: `readonly ExternalStoreThreadData<"archived">[] | undefined`
      - `onSwitchToNewThread?`: `(() => Promise<void> | void) | undefined` (experimental)
      - `onSwitchToThread?`: `((threadId: string) => Promise<void> | void) | undefined` (experimental)
      - `onRename?`: `( threadId: string, newTitle: string, ) => (Promise<void> | void) | undefined`
      - `onUpdateCustom?`: `(( threadId: string, custom: Record<string, unknown> | undefined, ) => Promise<void> | void) | undefined`
      - `onArchive?`: `((threadId: string) => Promise<void> | void) | undefined`
      - `onUnarchive?`: `((threadId: string) => Promise<void> | void) | undefined`
      - `onDelete?`: `((threadId: string) => Promise<void> | void) | undefined`

    - `history?`: `ThreadHistoryAdapter | undefined`

      - `scopeId?`: `string | undefined` — Stable identity for the storage scope read by LocalRuntime. Keep it the same when recreating the adapter for one thread, account, or workspace, and change it before loading a different scope. Adapters that omit it are treated as sharing one scope. External-history runtimes do not read it.
      - `unstable_copy?`: `(( branch: readonly ThreadMessage[], messageIds: readonly string[], ) => Promise<void>) | undefined` (experimental) — Keeps a copy of messages whose source of truth is the runtime's backend. \`branch\` is the conversation from its first message to its last, and \`messageIds\` names the ones in it that are new or changed; the adapter stores those, and first any earlier message of the branch it does not hold yet, keyed by each message's own id. It is undefined while the adapter keeps no copies, so the runtime then neither copies nor records tool interactions.
      - `load`: `() => Promise<ExportedMessageRepository & { state?: ReadonlyJSONValue; unstable_resume?: boolean; }>`
      - `resume?`: `(options: ChatModelRunOptions) => AsyncGenerator<ChatModelRunResult, void, unknown>`
      - `append`: `(item: ExportedMessageRepositoryItem) => Promise<void>`
      - `update?`: `(item: ExportedMessageRepositoryItem) => Promise<void>` — Rewrites a previously appended message in place, keyed by its message id. Adapters that implement this let a runtime persist a run paused for tool approval, finalize the same message once the run resumes, and record a tool result that arrives after the message settled, which can be a message later turns follow. Without it, a paused run is appended when it ends or when a later turn follows its message while the run is still open; anything that run adds after the append is not stored. An update may arrive for an id whose earlier write failed; treat it as an upsert keyed on the message id rather than assuming the entry exists.
      - `delete?`: `(items: ExportedMessageRepositoryItem[]) => Promise<void>` — Deletes messages from history. The runtime may send a second delete for the same message after a write it issued before the delete lands; treat deleting a missing entry as success.
      - `withFormat?`: `<TMessage, TStorageFormat extends Record<string, unknown>>(formatAdapter: MessageFormatAdapter<TMessage, TStorageFormat>) => GenericThreadHistoryAdapter<TMessage>` — Required when used with \`useAISDKRuntime\` / \`useChatRuntime\`.

    - `suggestion?`: `SuggestionAdapter | undefined`

      - `key?`: `string | number | symbol | undefined` — Changes when the adapter should regenerate suggestions for a settled run.
      - `generate`: `( options: SuggestionAdapterGenerateOptions, ) => | Promise<readonly ThreadSuggestion[]> | AsyncGenerator<readonly ThreadSuggestion[], void>`

  - `toCreateMessage?`: `CustomToCreateMessageFunction`

  - `onResume?`: `AISDKRuntimeAdapter["onResume"]`

  - `canResume?`: `AISDKRuntimeAdapter["canResume"]`

  - `onResumeToolCall?`: `AISDKRuntimeAdapter["onResumeToolCall"]`

  - `onRespondToToolApproval?`: `AISDKRuntimeAdapter["onRespondToToolApproval"]`

  - `onResumeError?`: `((error: unknown) => void) | undefined` — Called when a resumable stream reconnect fails. Use this to surface a toast, report telemetry, or mark the thread as needing a retry. With \`canResume: true\`, transient errors keep the stream id for manual retry. Otherwise, a failed reconnect clears the matching id. A 204 or 404 from the resume endpoint always clears the matching id.

  - `joinStrategy?`: `AISDKRuntimeAdapter["joinStrategy"]`

  - `messageRepository?`: `AISDKRuntimeAdapter<UI_MESSAGE>["messageRepository"]`

    - `headId?`: `string | null`
    - `messages`: `MessageFormatItem<TMessage>[]`

  - `unstable_onBranchChange?`: `AISDKRuntimeAdapter["unstable_onBranchChange"]` (experimental)

  - `unstable_enableMessageQueue?`: `AISDKRuntimeAdapter["unstable_enableMessageQueue"]` (experimental)

- `length`: `0 | 1`

- `toString`: `() => string`

- `toLocaleString`: `{ (): string; (locales: string | string[], options?: Intl.NumberFormatOptions & Intl.DateTimeFormatOptions): string; }`

- `pop`: `() => AISDKChatOptions<UI_MESSAGE>`

- `push`: `(...items: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => number`

- `concat`: `{ (...items: ConcatArray<AISDKChatOptions<UI_MESSAGE> | undefined>[]): (AISDKChatOptions<UI_MESSAGE> | undefined)[]; (...items: (AISDKChatOptions<UI_MESSAGE> | ConcatArray<AISDKChatOptions<UI_MESSAGE> | undefined> | undefined)[]): (AISDKChatOptions<UI_MESSAGE> | undefined)[]; }`

- `join`: `(separator?: string) => string`

- `reverse`: `() => (AISDKChatOptions<UI_MESSAGE> | undefined)[]`

- `shift`: `() => AISDKChatOptions<UI_MESSAGE>`

- `slice`: `(start?: number, end?: number) => (AISDKChatOptions<UI_MESSAGE> | undefined)[]`

- `sort`: `(compareFn?: ((a: AISDKChatOptions<UI_MESSAGE> | undefined, b: AISDKChatOptions<UI_MESSAGE> | undefined) => number) | undefined) => [options?: AISDKChatOptions<UI_MESSAGE> | undefined]`

- `splice`: `{ (start: number, deleteCount?: number): (AISDKChatOptions<UI_MESSAGE> | undefined)[]; (start: number, deleteCount: number, ...items: (AISDKChatOptions<UI_MESSAGE> | undefined)[]): (AISDKChatOptions<UI_MESSAGE> | undefined)[]; }`

- `unshift`: `(...items: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => number`

- `indexOf`: `(searchElement: AISDKChatOptions<UI_MESSAGE> | undefined, fromIndex?: number) => number`

- `lastIndexOf`: `(searchElement: AISDKChatOptions<UI_MESSAGE> | undefined, fromIndex?: number) => number`

- `every`: `{ <S>(predicate: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => value is S, thisArg?: any): this is S[]; (predicate: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any): boolean; }`

- `some`: `(predicate: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any) => boolean`

- `forEach`: `(callbackfn: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => void, thisArg?: any) => void`

- `map`: `<U>(callbackfn: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => U, thisArg?: any) => U[]`

- `filter`: `{ <S>(predicate: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => value is S, thisArg?: any): S[]; (predicate: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any): (AISDKChatOptions<UI_MESSAGE> | undefined)[]; }`

- `reduce`: `{ (callbackfn: (previousValue: AISDKChatOptions<UI_MESSAGE> | undefined, currentValue: AISDKChatOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => AISDKChatOptions<UI_MESSAGE> | undefined): AISDKChatOptions<UI_MESSAGE> | undefined; (callbackfn: (previousValue: AISDKChatOptions<UI_MESSAGE> | undefined, currentValue: AISDKChatOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => AISDKChatOptions<UI_MESSAGE> | undefined, initialValue: AISDKChatOptions<UI_MESSAGE> | undefined): AISDKChatOptions<UI_MESSAGE> | undefined; <U>(callbackfn: (previousValue: U, currentValue: AISDKChatOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => U, initialValue: U): U; }`

- `reduceRight`: `{ (callbackfn: (previousValue: AISDKChatOptions<UI_MESSAGE> | undefined, currentValue: AISDKChatOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => AISDKChatOptions<UI_MESSAGE> | undefined): AISDKChatOptions<UI_MESSAGE> | undefined; (callbackfn: (previousValue: AISDKChatOptions<UI_MESSAGE> | undefined, currentValue: AISDKChatOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => AISDKChatOptions<UI_MESSAGE> | undefined, initialValue: AISDKChatOptions<UI_MESSAGE> | undefined): AISDKChatOptions<UI_MESSAGE> | undefined; <U>(callbackfn: (previousValue: U, currentValue: AISDKChatOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => U, initialValue: U): U; }`

- `find`: `{ <S>(predicate: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, obj: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, obj: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any): AISDKChatOptions<UI_MESSAGE> | undefined; }`

- `findIndex`: `(predicate: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, obj: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any) => number`

- `fill`: `(value: AISDKChatOptions<UI_MESSAGE> | undefined, start?: number, end?: number) => [options?: AISDKChatOptions<UI_MESSAGE> | undefined]`

- `copyWithin`: `(target: number, start: number, end?: number) => [options?: AISDKChatOptions<UI_MESSAGE> | undefined]`

- `entries`: `() => ArrayIterator<[number, AISDKChatOptions<UI_MESSAGE> | undefined]>`

- `keys`: `() => ArrayIterator<number>`

- `values`: `() => ArrayIterator<AISDKChatOptions<UI_MESSAGE> | undefined>`

- `includes`: `(searchElement: AISDKChatOptions<UI_MESSAGE> | undefined, fromIndex?: number) => boolean`

- `flatMap`: `<U, This>(callback: (this: This, value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => U | readonly U[], thisArg?: This | undefined) => U[]`

- `flat`: `<A, D>(this: A, depth?: D | undefined) => FlatArray<A, D>[]`

- `at`: `(index: number) => AISDKChatOptions<UI_MESSAGE>`

- `findLast`: `{ <S>(predicate: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any): AISDKChatOptions<UI_MESSAGE> | undefined; }`

- `findLastIndex`: `(predicate: (value: AISDKChatOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKChatOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any) => number`

- `toReversed`: `() => (AISDKChatOptions<UI_MESSAGE> | undefined)[]`

- `toSorted`: `(compareFn?: ((a: AISDKChatOptions<UI_MESSAGE> | undefined, b: AISDKChatOptions<UI_MESSAGE> | undefined) => number) | undefined) => (AISDKChatOptions<UI_MESSAGE> | undefined)[]`

- `toSpliced`: `{ (start: number, deleteCount: number, ...items: (AISDKChatOptions<UI_MESSAGE> | undefined)[]): (AISDKChatOptions<UI_MESSAGE> | undefined)[]; (start: number, deleteCount?: number): (AISDKChatOptions<UI_MESSAGE> | undefined)[]; }`

- `with`: `(index: number, value: AISDKChatOptions<UI_MESSAGE> | undefined) => (AISDKChatOptions<UI_MESSAGE> | undefined)[]`

### AISDKThreads

`AuiConfig` entry that runs one AI SDK chat per thread. Hosts the same per-thread orchestration as [AISDKChat](/docs/api-reference/integrations/ai-sdk#aisdkchat) inside the client's own resource tree, so it works with any `AssistantClient` host, React or not. Without `cloud`, threads live in memory for the client's lifetime and keep their history across switches; only the visible thread is mounted, and a switched-away chat keeps streaming into its stored state until it settles or the thread is deleted. With `cloud`, the list is a `RemoteThreadList` with `backgroundThreads`: every visited thread stays mounted with its own history, a run continues after a switch and stops on delete, and a freshly created thread titles itself. Model context is registered on every mounted thread.

- `0?`: `AISDKThreadsOptions<UI_MESSAGE>`

  - `suggestions?`: `readonly ThreadSuggestion[] | undefined`

  - `onError?`: `ChatOnErrorCallback` — Callback function to be called when an error is encountered.

  - `isDisabled?`: `boolean | undefined` — Whether the entire thread is disabled. When \`true\`, the composer input cannot be typed into, attachments cannot be added, sends (including direct \`composer.send()\` calls) are blocked, and messages cannot be edited. For a narrower gate that keeps the input usable but blocks only sending, use \`isSendDisabled\`.

  - `isSendDisabled?`: `boolean | undefined` — Whether sending new messages is currently disabled. When \`true\`, the thread composer's input remains usable but \`send()\` becomes a no-op and the thread composer's \`canSend\` is \`false\`. Use this to gate sending on external React state (e.g. while tool config is loading) without disabling the input itself the way \`isDisabled\` does. Edit composers (saving message edits) intentionally ignore this flag.

  - `canResume?`: `AISDKRuntimeAdapter["canResume"]`

  - `messageRepository?`: `AISDKRuntimeAdapter<UI_MESSAGE>["messageRepository"]`

    - `headId?`: `string | null`
    - `messages`: `MessageFormatItem<TMessage>[]`

  - `unstable_onBranchChange?`: `AISDKRuntimeAdapter["unstable_onBranchChange"]` (experimental)

  - `onResume?`: `AISDKRuntimeAdapter["onResume"]`

  - `onResumeToolCall?`: `AISDKRuntimeAdapter["onResumeToolCall"]`

  - `onRespondToToolApproval?`: `AISDKRuntimeAdapter["onRespondToToolApproval"]`

  - `adapters?`: `AISDKRuntimeAdapter["adapters"] | undefined`

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

    - `threadList?`: `ExternalStoreThreadListAdapter | undefined` (experimental)

      - `threadId?`: `string | undefined` (experimental)
      - `isLoading?`: `boolean | undefined`
      - `threads?`: `readonly ExternalStoreThreadData<"regular">[] | undefined`
      - `archivedThreads?`: `readonly ExternalStoreThreadData<"archived">[] | undefined`
      - `onSwitchToNewThread?`: `(() => Promise<void> | void) | undefined` (experimental)
      - `onSwitchToThread?`: `((threadId: string) => Promise<void> | void) | undefined` (experimental)
      - `onRename?`: `( threadId: string, newTitle: string, ) => (Promise<void> | void) | undefined`
      - `onUpdateCustom?`: `(( threadId: string, custom: Record<string, unknown> | undefined, ) => Promise<void> | void) | undefined`
      - `onArchive?`: `((threadId: string) => Promise<void> | void) | undefined`
      - `onUnarchive?`: `((threadId: string) => Promise<void> | void) | undefined`
      - `onDelete?`: `((threadId: string) => Promise<void> | void) | undefined`

    - `history?`: `ThreadHistoryAdapter | undefined`

      - `scopeId?`: `string | undefined` — Stable identity for the storage scope read by LocalRuntime. Keep it the same when recreating the adapter for one thread, account, or workspace, and change it before loading a different scope. Adapters that omit it are treated as sharing one scope. External-history runtimes do not read it.
      - `unstable_copy?`: `(( branch: readonly ThreadMessage[], messageIds: readonly string[], ) => Promise<void>) | undefined` (experimental) — Keeps a copy of messages whose source of truth is the runtime's backend. \`branch\` is the conversation from its first message to its last, and \`messageIds\` names the ones in it that are new or changed; the adapter stores those, and first any earlier message of the branch it does not hold yet, keyed by each message's own id. It is undefined while the adapter keeps no copies, so the runtime then neither copies nor records tool interactions.
      - `load`: `() => Promise<ExportedMessageRepository & { state?: ReadonlyJSONValue; unstable_resume?: boolean; }>`
      - `resume?`: `(options: ChatModelRunOptions) => AsyncGenerator<ChatModelRunResult, void, unknown>`
      - `append`: `(item: ExportedMessageRepositoryItem) => Promise<void>`
      - `update?`: `(item: ExportedMessageRepositoryItem) => Promise<void>` — Rewrites a previously appended message in place, keyed by its message id. Adapters that implement this let a runtime persist a run paused for tool approval, finalize the same message once the run resumes, and record a tool result that arrives after the message settled, which can be a message later turns follow. Without it, a paused run is appended when it ends or when a later turn follows its message while the run is still open; anything that run adds after the append is not stored. An update may arrive for an id whose earlier write failed; treat it as an upsert keyed on the message id rather than assuming the entry exists.
      - `delete?`: `(items: ExportedMessageRepositoryItem[]) => Promise<void>` — Deletes messages from history. The runtime may send a second delete for the same message after a write it issued before the delete lands; treat deleting a missing entry as success.
      - `withFormat?`: `<TMessage, TStorageFormat extends Record<string, unknown>>(formatAdapter: MessageFormatAdapter<TMessage, TStorageFormat>) => GenericThreadHistoryAdapter<TMessage>` — Required when used with \`useAISDKRuntime\` / \`useChatRuntime\`.

    - `suggestion?`: `SuggestionAdapter | undefined`

      - `key?`: `string | number | symbol | undefined` — Changes when the adapter should regenerate suggestions for a settled run.
      - `generate`: `( options: SuggestionAdapterGenerateOptions, ) => | Promise<readonly ThreadSuggestion[]> | AsyncGenerator<readonly ThreadSuggestion[], void>`

  - `unstable_capabilities?`: `AISDKThreads props["0"]["unstable_capabilities"]` (experimental)
    - `copy?`: `boolean | undefined`

  - `unstable_enableMessageQueue?`: `AISDKRuntimeAdapter["unstable_enableMessageQueue"]` (experimental)

  - `onFinish?`: `ChatOnFinishCallback<UI_MESSAGE>` — Function that is called when the assistant response has finished streaming.

  - `joinStrategy?`: `AISDKRuntimeAdapter["joinStrategy"]`

  - `messageMetadataSchema?`: `FlexibleSchema$1<UI_MESSAGE['metadata']>`

  - `dataPartSchemas?`: `UIDataTypesToSchemas<InferUIMessageData<UI_MESSAGE>>`

  - `generateId?`: `IdGenerator$1` — A way to provide a function that is going to be used for ids for messages and the chat. If not provided the default AI SDK \`generateId\` is used.

  - `onToolCall?`: `ChatOnToolCallCallback<UI_MESSAGE>` — Optional callback function that is invoked when a tool call is received. Intended for automatic client-side tool execution. To add the tool output, call \`addToolOutput\` without awaiting it inside this callback. The callback's return value is not used.

  - `onData?`: `ChatOnDataCallback<UI_MESSAGE>` — Optional callback function that is called when a data part is received.

  - `sendAutomaticallyWhen?`: `(options: { messages: UI_MESSAGE[]; }) => boolean | PromiseLike<boolean>` — When provided, this function will be called when the stream is finished or a tool call is added to determine if the current messages should be resubmitted.

  - `throttle?`: `number | undefined`

  - `toCreateMessage?`: `CustomToCreateMessageFunction`

  - `onResumeError?`: `((error: unknown) => void) | undefined` — Called when a resumable stream reconnect fails. Use this to surface a toast, report telemetry, or mark the thread as needing a retry. With \`canResume: true\`, transient errors keep the stream id for manual retry. Otherwise, a failed reconnect clears the matching id. A 204 or 404 from the resume endpoint always clears the matching id.

  - `transport?`: `ChatTransport<UI_MESSAGE> | (() => ChatTransport<UI_MESSAGE>) | undefined` — The transport threads send through. A factory is invoked once per thread so each thread owns its instance. A plain \`AssistantChatTransport\` instance is cloned per thread (its assistant-ui wiring is per thread); any other transport instance is shared as-is. Defaults to one \`AssistantChatTransport\` per thread.

    - `resumeStreamIsReplay?`: `boolean` — Whether reconnecting returns the complete response from its beginning. Complete replays must reset the streaming message state before processing the first start chunk.
    - `close?`: `() => void` — Releases persistent resources owned by the transport. The caller that creates a transport owns its lifecycle and should call this method when no consumers remain. \`AbstractChat.dispose()\` calls it for directly managed chat instances.
    - `sendMessages`: `(options: { /** The type of message submission - either new message or regeneration */ trigger: 'submit-message' | 'regenerate-message'; /** Unique identifier for the chat session */ chatId: string; /** ID of the message to regenerate, or undefined for new messages */ messageId: string | undefined; /** Array of UI messages representing the conversation history */ messages: UI_MESSAGE[]; /** Signal to abort the request if needed */ abortSignal: AbortSignal | undefined; } & ChatRequestOptions) => Promise<ReadableStream<UIMessageChunk>>` — Sends messages to the chat API endpoint and returns a streaming response. This method handles both new message submission and message regeneration. It supports real-time streaming of responses through UIMessageChunk events.
    - `reconnectToStream`: `(options: { /** Unique identifier for the chat session to reconnect to */ chatId: string; /** Signal to abort the reconnection request if needed */ abortSignal?: AbortSignal; } & ChatRequestOptions) => Promise<ReadableStream<UIMessageChunk> | null>` — Reconnects to an existing streaming response for the specified chat session. This method is used to resume streaming when a connection is interrupted or when resuming a chat session. It's particularly useful for maintaining continuity in long-running conversations or recovering from network issues.

  - `cloud?`: `AssistantCloud | undefined` — When set, the thread list is a \`RemoteThreadList\` backed by this assistant-cloud. Omit it to keep the in-memory list. Every visited cloud thread stays mounted, so an in-flight run continues after a switch and stops on delete; per-thread history loads once per thread.

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

  - `threadId?`: `string | undefined` — Controlled thread id for the cloud list. Ignored without \`cloud\`.

  - `onThreadIdChange?`: `((threadId: string | undefined) => void) | undefined` — Called with the settled remote id when the cloud list changes thread.

- `length`: `0 | 1`

- `toString`: `() => string`

- `toLocaleString`: `{ (): string; (locales: string | string[], options?: Intl.NumberFormatOptions & Intl.DateTimeFormatOptions): string; }`

- `pop`: `() => AISDKThreadsOptions<UI_MESSAGE>`

- `push`: `(...items: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => number`

- `concat`: `{ (...items: ConcatArray<AISDKThreadsOptions<UI_MESSAGE> | undefined>[]): (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]; (...items: (AISDKThreadsOptions<UI_MESSAGE> | ConcatArray<AISDKThreadsOptions<UI_MESSAGE> | undefined> | undefined)[]): (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]; }`

- `join`: `(separator?: string) => string`

- `reverse`: `() => (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]`

- `shift`: `() => AISDKThreadsOptions<UI_MESSAGE>`

- `slice`: `(start?: number, end?: number) => (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]`

- `sort`: `(compareFn?: ((a: AISDKThreadsOptions<UI_MESSAGE> | undefined, b: AISDKThreadsOptions<UI_MESSAGE> | undefined) => number) | undefined) => [options?: AISDKThreadsOptions<UI_MESSAGE> | undefined]`

- `splice`: `{ (start: number, deleteCount?: number): (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]; (start: number, deleteCount: number, ...items: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]): (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]; }`

- `unshift`: `(...items: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => number`

- `indexOf`: `(searchElement: AISDKThreadsOptions<UI_MESSAGE> | undefined, fromIndex?: number) => number`

- `lastIndexOf`: `(searchElement: AISDKThreadsOptions<UI_MESSAGE> | undefined, fromIndex?: number) => number`

- `every`: `{ <S>(predicate: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => value is S, thisArg?: any): this is S[]; (predicate: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any): boolean; }`

- `some`: `(predicate: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any) => boolean`

- `forEach`: `(callbackfn: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => void, thisArg?: any) => void`

- `map`: `<U>(callbackfn: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => U, thisArg?: any) => U[]`

- `filter`: `{ <S>(predicate: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => value is S, thisArg?: any): S[]; (predicate: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any): (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]; }`

- `reduce`: `{ (callbackfn: (previousValue: AISDKThreadsOptions<UI_MESSAGE> | undefined, currentValue: AISDKThreadsOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => AISDKThreadsOptions<UI_MESSAGE> | undefined): AISDKThreadsOptions<UI_MESSAGE> | undefined; (callbackfn: (previousValue: AISDKThreadsOptions<UI_MESSAGE> | undefined, currentValue: AISDKThreadsOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => AISDKThreadsOptions<UI_MESSAGE> | undefined, initialValue: AISDKThreadsOptions<UI_MESSAGE> | undefined): AISDKThreadsOptions<UI_MESSAGE> | undefined; <U>(callbackfn: (previousValue: U, currentValue: AISDKThreadsOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => U, initialValue: U): U; }`

- `reduceRight`: `{ (callbackfn: (previousValue: AISDKThreadsOptions<UI_MESSAGE> | undefined, currentValue: AISDKThreadsOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => AISDKThreadsOptions<UI_MESSAGE> | undefined): AISDKThreadsOptions<UI_MESSAGE> | undefined; (callbackfn: (previousValue: AISDKThreadsOptions<UI_MESSAGE> | undefined, currentValue: AISDKThreadsOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => AISDKThreadsOptions<UI_MESSAGE> | undefined, initialValue: AISDKThreadsOptions<UI_MESSAGE> | undefined): AISDKThreadsOptions<UI_MESSAGE> | undefined; <U>(callbackfn: (previousValue: U, currentValue: AISDKThreadsOptions<UI_MESSAGE> | undefined, currentIndex: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => U, initialValue: U): U; }`

- `find`: `{ <S>(predicate: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, obj: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, obj: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any): AISDKThreadsOptions<UI_MESSAGE> | undefined; }`

- `findIndex`: `(predicate: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, obj: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any) => number`

- `fill`: `(value: AISDKThreadsOptions<UI_MESSAGE> | undefined, start?: number, end?: number) => [options?: AISDKThreadsOptions<UI_MESSAGE> | undefined]`

- `copyWithin`: `(target: number, start: number, end?: number) => [options?: AISDKThreadsOptions<UI_MESSAGE> | undefined]`

- `entries`: `() => ArrayIterator<[number, AISDKThreadsOptions<UI_MESSAGE> | undefined]>`

- `keys`: `() => ArrayIterator<number>`

- `values`: `() => ArrayIterator<AISDKThreadsOptions<UI_MESSAGE> | undefined>`

- `includes`: `(searchElement: AISDKThreadsOptions<UI_MESSAGE> | undefined, fromIndex?: number) => boolean`

- `flatMap`: `<U, This>(callback: (this: This, value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => U | readonly U[], thisArg?: This | undefined) => U[]`

- `flat`: `<A, D>(this: A, depth?: D | undefined) => FlatArray<A, D>[]`

- `at`: `(index: number) => AISDKThreadsOptions<UI_MESSAGE>`

- `findLast`: `{ <S>(predicate: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any): AISDKThreadsOptions<UI_MESSAGE> | undefined; }`

- `findLastIndex`: `(predicate: (value: AISDKThreadsOptions<UI_MESSAGE> | undefined, index: number, array: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]) => unknown, thisArg?: any) => number`

- `toReversed`: `() => (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]`

- `toSorted`: `(compareFn?: ((a: AISDKThreadsOptions<UI_MESSAGE> | undefined, b: AISDKThreadsOptions<UI_MESSAGE> | undefined) => number) | undefined) => (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]`

- `toSpliced`: `{ (start: number, deleteCount: number, ...items: (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]): (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]; (start: number, deleteCount?: number): (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]; }`

- `with`: `(index: number, value: AISDKThreadsOptions<UI_MESSAGE> | undefined) => (AISDKThreadsOptions<UI_MESSAGE> | undefined)[]`

### AISDKToolkit

- `constructor?`: `(options: AISDKToolkitOptions) => AISDKToolkit`
- `#toolkit?`: `Toolkit`
- `#mcpClients?`: `Map<string, Promise<MCPClient>>`
- `tools?`: `(options: AISDKToolkitToolsOptions = {}) => Promise<ToolSet>`
- `close?`: `() => Promise<void>`
- `#mcpTools?`: `() => Promise<McpToolSet>`
- `#mcpClient?`: `(name: string, config: McpServerConfig, startedAt: number) => Promise<MCPClient>`

### AssistantChatTransport

- `constructor?`: `(initOptions?: AssistantChatTransportInitOptions<UI_MESSAGE>) => AssistantChatTransport`
- `__internal_clone?`: `() => AssistantChatTransport<UI_MESSAGE>` — Constructs an unwired copy with the same init options.
- `setRuntime?`: `(runtime: AssistantRuntime) => void`
- `getResumableAdapter?`: `() => AssistantChatResumableOptions | undefined`
- `__internal_setGetThreadListItem?`: `(getter: () => InitializableThreadListItem | undefined) => void`

### createResumableSessionStorage

`sessionStorage`-backed storage for the pending resumable stream id. See the [Resumable Streams](/docs/guides/resumable-streams) guide for end-to-end wiring.

- `options?`: `{ key?: string | (() => string | undefined); }`
  - `key?`: `string | (() => string | undefined)` — Storage key for the pending stream id. A static string namespaces per route or chat surface. A getter is read lazily on every access, so the key can be derived from the active thread's identity; while the getter returns \`undefined\`, reads report no pending stream and writes are dropped, so a thread whose identity is not known yet never touches another thread's key. Under a remote thread list with more than one thread, scope the key per thread and create one storage instance per thread runtime rather than a single shared one. A shared key is written and cleared by whichever thread acts last, so one conversation's stream can resume inside another.

### frontendTools

```
const frontendTools: (tools: FrontendTools) => ToolSet;
```

### getThreadMessageTokenUsage

- `message`: `TokenUsageExtractableMessage`

  - `role?`: `string`
  - `metadata?`: `unknown`

### injectQuoteContext

Injects quote context into messages as markdown blockquotes.

Use this in your route handler before `convertToModelMessages` so the LLM sees the quoted text that the user is referring to.

```
import { convertToModelMessages, streamText } from "ai";
import { injectQuoteContext } from "@assistant-ui/ai-sdk";

export async function POST(req: Request) {
  const { messages } = await req.json();
  const result = streamText({
    abortSignal: req.signal,
    model: myModel,
    messages: await convertToModelMessages(injectQuoteContext(messages)),
  });
  return result.toUIMessageStreamResponse();
}
```

- `messages`: `UIMessage<unknown, UIDataTypes, UITools>[]`

### RESUMABLE_STREAM_ID_HEADER

Response header used by the [Resumable Streams](/docs/guides/resumable-streams) server and client wiring.

```
const RESUMABLE_STREAM_ID_HEADER: "x-resumable-stream-id";
```

### useAISDKChat

The underlying `useChat` helpers object, for advanced views — reach `resumeStream`, `clearError`, or the raw `sendMessage` without forking the runtime hook. `undefined` when the current thread is not backed by the AI SDK runtime.

```
const useAISDKChat: <UI_MESSAGE extends UIMessage = UIMessage<unknown, UIDataTypes, UITools>>() => UseChatHelpers<UI_MESSAGE> | undefined;
```

### useAISDKError

Read the last AI SDK chat error object from the runtime extras. `undefined` when there is no error or the current thread is not backed by the AI SDK runtime.

```
const useAISDKError: () => Error | undefined;
```

### useAISDKRuntime

- `chatHelpers`: `UseChatHelpers<UI_MESSAGE>`

  - `id`: `string` — The id of the chat.

  - `setMessages`: `(messages: UI_MESSAGE[] | ((messages: UI_MESSAGE[]) => UI_MESSAGE[])) => void` — Update the \`messages\` state locally. This is useful when you want to edit the messages on the client, and then trigger the \`reload\` method manually to regenerate the AI response.

  - `error?`: `Error | undefined`

    - `name`: `string`
    - `message`: `string`
    - `stack?`: `string`
    - `cause?`: `unknown`

  - `status`: `ChatStatus` — Hook status: - \`submitted\`: The message has been sent to the API and we're awaiting the start of the response stream. - \`streaming\`: The response is actively streaming in from the API, receiving chunks of data. - \`ready\`: The full response has been received and processed; a new user message can be submitted. - \`error\`: An error occurred during the API request, preventing successful completion.

  - `addToolResult`: `ChatAddToolOutputFunction<UI_MESSAGE>` (deprecated: Use addToolOutput)

  - `stop`: `() => Promise<void>` — Abort the current request, keep the generated tokens if any, and wait for the request pipeline to finish.

  - `messages`: `UI_MESSAGE[]`

  - `sendMessage`: `(message?: (CreateUIMessage<UI_MESSAGE> & { text?: never; files?: never; messageId?: string; }) | { text: string; files?: FileList | FileUIPart[]; metadata?: InferUIMessageMetadata<UI_MESSAGE>; parts?: never; messageId?: string; } | { files: FileList | FileUIPart[]; metadata?: InferUIMessageMetadata<UI_MESSAGE>; parts?: never; messageId?: string; }, options?: ChatRequestOptions) => Promise<void>` — Appends or replaces a user message to the chat list. This triggers the API call to fetch the assistant's response. If a messageId is provided, the message will be replaced.

  - `regenerate`: `({ messageId, ...options }?: { messageId?: string; } & ChatRequestOptions) => Promise<void>` — Regenerate the assistant message with the provided message id. If no message id is provided, the last assistant message will be regenerated.

  - `resumeStream`: `(options?: ChatRequestOptions) => Promise<void>` — Attempt to resume an ongoing streaming response.

  - `addToolOutput`: `ChatAddToolOutputFunction<UI_MESSAGE>`

  - `addToolApprovalResponse`: `ChatAddToolApproveResponseFunction`

  - `clearError`: `() => void` — Clear the error state and set the status to ready if the chat is in an error state.

- `adapter?`: `AISDKRuntimeAdapter<UI_MESSAGE>`

  - `suggestions?`: `readonly ThreadSuggestion[] | undefined`

  - `isDisabled?`: `boolean | undefined` — Whether the entire thread is disabled. When \`true\`, the composer input cannot be typed into, attachments cannot be added, sends (including direct \`composer.send()\` calls) are blocked, and messages cannot be edited. For a narrower gate that keeps the input usable but blocks only sending, use \`isSendDisabled\`.

  - `isSendDisabled?`: `boolean | undefined` — Whether sending new messages is currently disabled. When \`true\`, the thread composer's input remains usable but \`send()\` becomes a no-op and the thread composer's \`canSend\` is \`false\`. Use this to gate sending on external React state (e.g. while tool config is loading) without disabling the input itself the way \`isDisabled\` does. Edit composers (saving message edits) intentionally ignore this flag.

  - `unstable_capabilities?`: `AISDKRuntimeAdapter["unstable_capabilities"]` (experimental)
    - `copy?`: `boolean | undefined`

  - `adapters?`: `(NonNullable<ExternalStoreAdapter["adapters"]> & { history?: ThreadHistoryAdapter | undefined; suggestion?: SuggestionAdapter | undefined; }) | undefined`

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

    - `threadList?`: `ExternalStoreThreadListAdapter | undefined` (experimental)

      - `threadId?`: `string | undefined` (experimental)
      - `isLoading?`: `boolean | undefined`
      - `threads?`: `readonly ExternalStoreThreadData<"regular">[] | undefined`
      - `archivedThreads?`: `readonly ExternalStoreThreadData<"archived">[] | undefined`
      - `onSwitchToNewThread?`: `(() => Promise<void> | void) | undefined` (experimental)
      - `onSwitchToThread?`: `((threadId: string) => Promise<void> | void) | undefined` (experimental)
      - `onRename?`: `( threadId: string, newTitle: string, ) => (Promise<void> | void) | undefined`
      - `onUpdateCustom?`: `(( threadId: string, custom: Record<string, unknown> | undefined, ) => Promise<void> | void) | undefined`
      - `onArchive?`: `((threadId: string) => Promise<void> | void) | undefined`
      - `onUnarchive?`: `((threadId: string) => Promise<void> | void) | undefined`
      - `onDelete?`: `((threadId: string) => Promise<void> | void) | undefined`

    - `history?`: `ThreadHistoryAdapter | undefined`

      - `scopeId?`: `string | undefined` — Stable identity for the storage scope read by LocalRuntime. Keep it the same when recreating the adapter for one thread, account, or workspace, and change it before loading a different scope. Adapters that omit it are treated as sharing one scope. External-history runtimes do not read it.
      - `unstable_copy?`: `(( branch: readonly ThreadMessage[], messageIds: readonly string[], ) => Promise<void>) | undefined` (experimental) — Keeps a copy of messages whose source of truth is the runtime's backend. \`branch\` is the conversation from its first message to its last, and \`messageIds\` names the ones in it that are new or changed; the adapter stores those, and first any earlier message of the branch it does not hold yet, keyed by each message's own id. It is undefined while the adapter keeps no copies, so the runtime then neither copies nor records tool interactions.
      - `load`: `() => Promise<ExportedMessageRepository & { state?: ReadonlyJSONValue; unstable_resume?: boolean; }>`
      - `resume?`: `(options: ChatModelRunOptions) => AsyncGenerator<ChatModelRunResult, void, unknown>`
      - `append`: `(item: ExportedMessageRepositoryItem) => Promise<void>`
      - `update?`: `(item: ExportedMessageRepositoryItem) => Promise<void>` — Rewrites a previously appended message in place, keyed by its message id. Adapters that implement this let a runtime persist a run paused for tool approval, finalize the same message once the run resumes, and record a tool result that arrives after the message settled, which can be a message later turns follow. Without it, a paused run is appended when it ends or when a later turn follows its message while the run is still open; anything that run adds after the append is not stored. An update may arrive for an id whose earlier write failed; treat it as an upsert keyed on the message id rather than assuming the entry exists.
      - `delete?`: `(items: ExportedMessageRepositoryItem[]) => Promise<void>` — Deletes messages from history. The runtime may send a second delete for the same message after a write it issued before the delete lands; treat deleting a missing entry as success.
      - `withFormat?`: `<TMessage, TStorageFormat extends Record<string, unknown>>(formatAdapter: MessageFormatAdapter<TMessage, TStorageFormat>) => GenericThreadHistoryAdapter<TMessage>` — Required when used with \`useAISDKRuntime\` / \`useChatRuntime\`.

    - `suggestion?`: `SuggestionAdapter | undefined`

      - `key?`: `string | number | symbol | undefined` — Changes when the adapter should regenerate suggestions for a settled run.
      - `generate`: `( options: SuggestionAdapterGenerateOptions, ) => | Promise<readonly ThreadSuggestion[]> | AsyncGenerator<readonly ThreadSuggestion[], void>`

  - `toCreateMessage?`: `CustomToCreateMessageFunction`

  - `unstable_messageRepositoryInstance?`: `MessageRepository | undefined` (experimental)

    - `messages`: `Map<string, RepositoryMessage>`

      - `clear`: `() => void`
      - `delete`: `(key: string) => boolean`
      - `forEach`: `(callbackfn: (value: { children: string[]; next: (RepositoryParent & { prev: (RepositoryParent & any) | null; current: ThreadMessage; level: number; }) | null; } & { prev: ({ children: string[]; next: (RepositoryParent & any) | null; } & any) | null; current: ThreadMessage; level: number; }, key: string, map: Map<string, { children: string[]; next: (RepositoryParent & { prev: (RepositoryParent & any) | null; current: ThreadMessage; level: number; }) | null; } & { prev: ({ children: string[]; next: (RepositoryParent & any) | null; } & any) | null; current: ThreadMessage; level: number; }>) => void, thisArg?: any) => void`
      - `get`: `(key: string) => ({ children: string[]; next: (RepositoryParent & { prev: (RepositoryParent & any) | null; current: ThreadMessage; level: number; }) | null; } & { prev: ({ children: string[]; next: (RepositoryParent & any) | null; } & any) | null; current: ThreadMessage; level: number; })`
      - `has`: `(key: string) => boolean`
      - `set`: `(key: string, value: { children: string[]; next: (RepositoryParent & { prev: (RepositoryParent & any) | null; current: ThreadMessage; level: number; }) | null; } & { prev: ({ children: string[]; next: (RepositoryParent & any) | null; } & any) | null; current: ThreadMessage; level: number; }) => Map<string, { children: string[]; next: (RepositoryParent & { prev: (RepositoryParent & any) | null; current: ThreadMessage; level: number; }) | null; } & { prev: ({ children: string[]; next: (RepositoryParent & any) | null; } & any) | null; current: ThreadMessage; level: number; }>`
      - `size`: `number`
      - `entries`: `() => MapIterator<[string, { children: string[]; next: (RepositoryParent & { prev: (RepositoryParent & any) | null; current: ThreadMessage; level: number; }) | null; } & { prev: ({ children: string[]; next: (RepositoryParent & any) | null; } & any) | null; current: ThreadMessage; level: number; }]>`
      - `keys`: `() => MapIterator<string>`
      - `values`: `() => MapIterator<{ children: string[]; next: (RepositoryParent & { prev: (RepositoryParent & any) | null; current: ThreadMessage; level: number; }) | null; } & { prev: ({ children: string[]; next: (RepositoryParent & any) | null; } & any) | null; current: ThreadMessage; level: number; }>`

    - `head`: `RepositoryMessage | null`

      - `children`: `string[]`
      - `next`: `RepositoryMessage | null`
      - `prev`: `RepositoryMessage | null`
      - `current`: `ThreadMessage`
      - `level`: `number`

    - `root`: `RepositoryParent`

      - `children`: `string[]`
      - `next`: `RepositoryMessage | null`

    - `updateLevels`: `(message: RepositoryMessage, newLevel: number) => void`

    - `selectPathTo`: `(message: RepositoryMessage) => void`

    - `performOp`: `(newParent: RepositoryMessage | null, child: RepositoryMessage, operation: "cut" | "link" | "relink") => void`

    - `_messages`: `CachedValue<readonly ThreadMessage[]>`

      - `_value`: `T | null`
      - `func`: `() => T`
      - `value`: `readonly ThreadMessage[]`
      - `dirty`: `() => void`

    - `headId`: `string | null`

    - `canonicalHeadId`: `string | null`

    - `getMessages`: `(headId?: string) => readonly ThreadMessage[]`

    - `addOrUpdateMessage`: `(parentId: string | null, message: ThreadMessage) => void`

    - `getMessage`: `(messageId: string) => { parentId: string | null; message: ThreadMessage; index: number; }`

    - `deleteMessage`: `(messageId: string, replacementId?: string | null | undefined) => void`

    - `hasChildren`: `(messageId: string) => boolean`

    - `getBranches`: `(messageId: string) => string[]`

    - `evictOffBranchOptimisticMessages`: `(previousHead: RepositoryMessage | null, currentHead: RepositoryMessage | null) => void` — Evicts optimistic messages (\`metadata.isOptimistic\`) on the branch the head just moved away from. Only that branch is walked, so an optimistic message added off the head branch (such as the server-id copy that replaces a client-id placeholder before the head moves to it) is kept until a \`switchToBranch\` or \`resetHead\` moves the head off the branch it is on. Keeps a client→server id swap from leaving a phantom sibling.

    - `switchToBranch`: `(messageId: string) => void`

    - `setHead`: `(messageId: string | null, pruneChildren: boolean) => void`

    - `resetHead`: `(messageId: string | null) => void`

    - `clear`: `() => void`

    - `export`: `() => ExportedMessageRepository`

    - `exportedHeadId`: `() => string | null` — The head as it would be if the optimistic messages were deleted. An optimistic head resolves to a leaf of the exported tree, so an import returns to the branch that was visible instead of the one its persisted ancestor selects.

    - `exportedChild`: `(parent: RepositoryParent) => RepositoryMessage | null`

    - `import`: `({ headId, messages }: ExportedMessageRepository) => void`

  - `unstable_hostApprovalOwner?`: `object | undefined` (experimental) — The object a host answer belongs to, normally the \`Chat\` the runtime renders. A host answer never reaches the \`useChat\` messages, so without an owner it lives only as long as this runtime. With an owner, it survives runtime remounts over the same chat.

  - `cancelPendingToolCallsOnSend`: `boolean | undefined` (default `true`) — Whether to automatically cancel pending interactive tool calls when the user sends a new message. When enabled (default), the pending tool calls will be marked as failed with an error message indicating the user cancelled the tool call by sending a new message.

  - `unstable_enableMessageQueue?`: `boolean | undefined` (experimental) — Holds messages sent during a run in \`composer.queue\` until the run settles. Steered messages dispatch before queued follow-ups.

  - `onResume?`: `ExternalStoreAdapter["onResume"]` — Called when \`runtime.thread.resumeRun(config)\` is invoked. When omitted, \`resumeRun\` throws \`"Runtime does not support resuming runs."\`. Provide this to bridge resume invocations into a custom replay channel (for example, an SSE reconnect endpoint keyed by turn id).

  - `canResume?`: `ExternalStoreAdapter["canResume"]`

  - `onResumeToolCall?`: `ExternalStoreAdapter["onResumeToolCall"]` — Called when \`runtime.thread.resumeToolCall(options)\` is invoked for a tool call the in-process tracker does not own. When omitted, \`resumeToolCall\` throws \`"Tool call ${toolCallId} is not waiting for resume."\`. Provide this to bridge resume-tool-call invocations into a custom handler.

  - `onRespondToToolApproval?`: ``(( response: RespondToToolApprovalOptions, context: { toolCallId: string; toolName: string; /** Sends this response through the AI SDK's `addToolApprovalResponse`, which carries only `approved` and `reason`. */ respondViaAISDK: () => Promise<void>; }, ) => Promise<void> | void) | undefined`` — Answers tool approval requests through a host-owned channel instead of the AI SDK's \`addToolApprovalResponse\`. Called for every approval request in the thread with the complete response, including option and free-form answers. Hand requests the host does not own to \`respondViaAISDK\`, which is what runs when this option is omitted. The answer applies to the approval when the handler starts and is removed if it throws. It is never written into the \`useChat\` messages, so \`sendAutomaticallyWhen\` cannot forward it. With a history adapter, the answer is stored with its message once the handler resolves and returns on reload. With \`unstable_hostApprovalOwner\`, in-memory answers survive runtime remounts over the same chat; without an owner or history adapter, they last only as long as the runtime. Bring the resumed run back into the chat, for example with \`resumeStream\`, and have the endpoint refuse a second resume. While a handler is set, an approval's \`display\`, \`allowFreeform\`, \`dismissible\` and \`options\` reach the renderer, because the handler can receive answers the AI SDK cannot carry. A stream declares them through the \`approvalDescriptor\` of its \`tool-approval-request\` chunk, the one approval field the AI SDK keeps opaque; the converter reads the request and answer fields from that descriptor when the approval itself lacks them.

  - `joinStrategy?`: `JoinStrategy | undefined` — How consecutive assistant messages are rendered. \`"concat-content"\` (the default) merges them into a single thread message. \`"none"\` keeps each assistant message as its own thread message, which is useful when a backend persists proactive or consecutive assistant messages as separate entries.

  - `messageRepository?`: `MessageFormatRepository<UI_MESSAGE>` — A branch-aware AI SDK message tree seeded once when \`useChat\` is empty. After that seed, live updates come only from \`useChat\`. A later empty chat or a new object identity does not reload the tree.

    - `headId?`: `string | null`
    - `messages`: `MessageFormatItem<TMessage>[]`

  - `unstable_onBranchChange?`: `ExternalStoreAdapter["unstable_onBranchChange"]` (experimental) — Called after an explicit \`switchToBranch\` (for example a BranchPicker click). Complements \`setMessages\` and does not enable switching by itself.

### useChatRuntime

- `options?`: `UseChatRuntimeOptions<UI_MESSAGE>`

  - `suggestions?`: `readonly ThreadSuggestion[] | undefined`

  - `id?`: `string` — A unique identifier for the chat. If not provided, a random one will be generated when first accessed.

  - `onError?`: `ChatOnErrorCallback` — Callback function to be called when an error is encountered.

  - `messages?`: `UI_MESSAGE[]`

  - `isDisabled?`: `boolean | undefined` — Whether the entire thread is disabled. When \`true\`, the composer input cannot be typed into, attachments cannot be added, sends (including direct \`composer.send()\` calls) are blocked, and messages cannot be edited. For a narrower gate that keeps the input usable but blocks only sending, use \`isSendDisabled\`.

  - `isSendDisabled?`: `boolean | undefined` — Whether sending new messages is currently disabled. When \`true\`, the thread composer's input remains usable but \`send()\` becomes a no-op and the thread composer's \`canSend\` is \`false\`. Use this to gate sending on external React state (e.g. while tool config is loading) without disabling the input itself the way \`isDisabled\` does. Edit composers (saving message edits) intentionally ignore this flag.

  - `canResume?`: `AISDKRuntimeAdapter["canResume"]`

  - `messageRepository?`: `AISDKRuntimeAdapter<UI_MESSAGE>["messageRepository"]`

    - `headId?`: `string | null`
    - `messages`: `MessageFormatItem<TMessage>[]`

  - `unstable_onBranchChange?`: `AISDKRuntimeAdapter["unstable_onBranchChange"]` (experimental)

  - `onResume?`: `AISDKRuntimeAdapter["onResume"]`

  - `onResumeToolCall?`: `AISDKRuntimeAdapter["onResumeToolCall"]`

  - `onRespondToToolApproval?`: `AISDKRuntimeAdapter["onRespondToToolApproval"]`

  - `adapters?`: `AISDKRuntimeAdapter["adapters"] | undefined`

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

    - `threadList?`: `ExternalStoreThreadListAdapter | undefined` (experimental)

      - `threadId?`: `string | undefined` (experimental)
      - `isLoading?`: `boolean | undefined`
      - `threads?`: `readonly ExternalStoreThreadData<"regular">[] | undefined`
      - `archivedThreads?`: `readonly ExternalStoreThreadData<"archived">[] | undefined`
      - `onSwitchToNewThread?`: `(() => Promise<void> | void) | undefined` (experimental)
      - `onSwitchToThread?`: `((threadId: string) => Promise<void> | void) | undefined` (experimental)
      - `onRename?`: `( threadId: string, newTitle: string, ) => (Promise<void> | void) | undefined`
      - `onUpdateCustom?`: `(( threadId: string, custom: Record<string, unknown> | undefined, ) => Promise<void> | void) | undefined`
      - `onArchive?`: `((threadId: string) => Promise<void> | void) | undefined`
      - `onUnarchive?`: `((threadId: string) => Promise<void> | void) | undefined`
      - `onDelete?`: `((threadId: string) => Promise<void> | void) | undefined`

    - `history?`: `ThreadHistoryAdapter | undefined`

      - `scopeId?`: `string | undefined` — Stable identity for the storage scope read by LocalRuntime. Keep it the same when recreating the adapter for one thread, account, or workspace, and change it before loading a different scope. Adapters that omit it are treated as sharing one scope. External-history runtimes do not read it.
      - `unstable_copy?`: `(( branch: readonly ThreadMessage[], messageIds: readonly string[], ) => Promise<void>) | undefined` (experimental) — Keeps a copy of messages whose source of truth is the runtime's backend. \`branch\` is the conversation from its first message to its last, and \`messageIds\` names the ones in it that are new or changed; the adapter stores those, and first any earlier message of the branch it does not hold yet, keyed by each message's own id. It is undefined while the adapter keeps no copies, so the runtime then neither copies nor records tool interactions.
      - `load`: `() => Promise<ExportedMessageRepository & { state?: ReadonlyJSONValue; unstable_resume?: boolean; }>`
      - `resume?`: `(options: ChatModelRunOptions) => AsyncGenerator<ChatModelRunResult, void, unknown>`
      - `append`: `(item: ExportedMessageRepositoryItem) => Promise<void>`
      - `update?`: `(item: ExportedMessageRepositoryItem) => Promise<void>` — Rewrites a previously appended message in place, keyed by its message id. Adapters that implement this let a runtime persist a run paused for tool approval, finalize the same message once the run resumes, and record a tool result that arrives after the message settled, which can be a message later turns follow. Without it, a paused run is appended when it ends or when a later turn follows its message while the run is still open; anything that run adds after the append is not stored. An update may arrive for an id whose earlier write failed; treat it as an upsert keyed on the message id rather than assuming the entry exists.
      - `delete?`: `(items: ExportedMessageRepositoryItem[]) => Promise<void>` — Deletes messages from history. The runtime may send a second delete for the same message after a write it issued before the delete lands; treat deleting a missing entry as success.
      - `withFormat?`: `<TMessage, TStorageFormat extends Record<string, unknown>>(formatAdapter: MessageFormatAdapter<TMessage, TStorageFormat>) => GenericThreadHistoryAdapter<TMessage>` — Required when used with \`useAISDKRuntime\` / \`useChatRuntime\`.

    - `suggestion?`: `SuggestionAdapter | undefined`

      - `key?`: `string | number | symbol | undefined` — Changes when the adapter should regenerate suggestions for a settled run.
      - `generate`: `( options: SuggestionAdapterGenerateOptions, ) => | Promise<readonly ThreadSuggestion[]> | AsyncGenerator<readonly ThreadSuggestion[], void>`

  - `unstable_capabilities?`: `UseChatRuntimeOptions["unstable_capabilities"]` (experimental)
    - `copy?`: `boolean | undefined`

  - `unstable_enableMessageQueue?`: `AISDKRuntimeAdapter["unstable_enableMessageQueue"]` (experimental)

  - `onFinish?`: `ChatOnFinishCallback<UI_MESSAGE>` — Function that is called when the assistant response has finished streaming.

  - `joinStrategy?`: `AISDKRuntimeAdapter["joinStrategy"]`

  - `messageMetadataSchema?`: `FlexibleSchema$1<UI_MESSAGE['metadata']>`

  - `dataPartSchemas?`: `UIDataTypesToSchemas<InferUIMessageData<UI_MESSAGE>>`

  - `generateId?`: `IdGenerator$1` — A way to provide a function that is going to be used for ids for messages and the chat. If not provided the default AI SDK \`generateId\` is used.

  - `onToolCall?`: `ChatOnToolCallCallback<UI_MESSAGE>` — Optional callback function that is invoked when a tool call is received. Intended for automatic client-side tool execution. To add the tool output, call \`addToolOutput\` without awaiting it inside this callback. The callback's return value is not used.

  - `onData?`: `ChatOnDataCallback<UI_MESSAGE>` — Optional callback function that is called when a data part is received.

  - `sendAutomaticallyWhen?`: `(options: { messages: UI_MESSAGE[]; }) => boolean | PromiseLike<boolean>` — When provided, this function will be called when the stream is finished or a tool call is added to determine if the current messages should be resubmitted.

  - `throttle?`: `number | undefined`

  - `toCreateMessage?`: `CustomToCreateMessageFunction`

  - `onResumeError?`: `((error: unknown) => void) | undefined` — Called when a resumable stream reconnect fails. Use this to surface a toast, report telemetry, or mark the thread as needing a retry. With \`canResume: true\`, transient errors keep the stream id for manual retry. Otherwise, a failed reconnect clears the matching id. A 204 or 404 from the resume endpoint always clears the matching id.

  - `transport?`: `ChatTransport<UI_MESSAGE> | undefined` — The transport threads send through. \`AssistantChatTransport\` instances are cloned per thread through \`\_\_internal_clone()\` so their assistant-ui wiring remains isolated. Other transport instances are shared as-is.

    - `resumeStreamIsReplay?`: `boolean` — Whether reconnecting returns the complete response from its beginning. Complete replays must reset the streaming message state before processing the first start chunk.
    - `close?`: `() => void` — Releases persistent resources owned by the transport. The caller that creates a transport owns its lifecycle and should call this method when no consumers remain. \`AbstractChat.dispose()\` calls it for directly managed chat instances.
    - `sendMessages`: `(options: { /** The type of message submission - either new message or regeneration */ trigger: 'submit-message' | 'regenerate-message'; /** Unique identifier for the chat session */ chatId: string; /** ID of the message to regenerate, or undefined for new messages */ messageId: string | undefined; /** Array of UI messages representing the conversation history */ messages: UI_MESSAGE[]; /** Signal to abort the request if needed */ abortSignal: AbortSignal | undefined; } & ChatRequestOptions) => Promise<ReadableStream<UIMessageChunk>>` — Sends messages to the chat API endpoint and returns a streaming response. This method handles both new message submission and message regeneration. It supports real-time streaming of responses through UIMessageChunk events.
    - `reconnectToStream`: `(options: { /** Unique identifier for the chat session to reconnect to */ chatId: string; /** Signal to abort the reconnection request if needed */ abortSignal?: AbortSignal; } & ChatRequestOptions) => Promise<ReadableStream<UIMessageChunk> | null>` — Reconnects to an existing streaming response for the specified chat session. This method is used to resume streaming when a connection is interrupted or when resuming a chat session. It's particularly useful for maintaining continuity in long-running conversations or recovering from network issues.

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

  - `onThreadIdChange?`: `((threadId: string | undefined) => void) | undefined`

### useThreadTokenUsage

Reads token usage from the newest assistant message that reports any.

A route attaches usage through the AI SDK's `messageMetadata` option. Because a thread message carries a fixed metadata shape, the converter moves every other key the route returns into `metadata.custom`, which is where this hook looks.

```
function useThreadTokenUsage(): ThreadTokenUsage | undefined;
```

### unstable_injectInteractableContext

> [!tip]
>
> **Experimental.** Shipped 2026-06-23; it may change in any release.

Injects interactable state snapshots into messages as model-visible text.

Mirrors [injectQuoteContext](/docs/api-reference/integrations/ai-sdk#injectquotecontext): reads the frozen snapshot stamped on a user message's `metadata.custom.interactables` (by the interactables scope at send time) and prepends a text part. Run this in your route handler before `convertToModelMessages`, which otherwise ignores `metadata.custom`.

Wording is consumer-owned — pass `format` to control how each snapshot reads. A snapshot may originate from a user edit or an agent `update_*` call, so the default phrasing is neutral. Keep the instance id visible in custom wording: the model needs it to address the `update_*` tool's `id` parameter. A custom `format` must also handle entries with `partial: true`, whose `state` carries only the fields that changed since the model's last known state.

```
import { convertToModelMessages, streamText } from "ai";
import { unstable_injectInteractableContext } from "@assistant-ui/ai-sdk";

export async function POST(req: Request) {
  const { messages } = await req.json();
  const result = streamText({
    abortSignal: req.signal,
    model: myModel,
    messages: await convertToModelMessages(unstable_injectInteractableContext(messages)),
  });
  return result.toUIMessageStreamResponse();
}
```

- `messages`: `UIMessage<unknown, UIDataTypes, UITools>[]`
- `format?`: `(item: Unstable_InteractableSnapshotEntry) => string`

### generativeTools

> [!warn]
>
> **Deprecated.** Use [AISDKToolkit](/docs/api-reference/integrations/ai-sdk#aisdktoolkit) instead: `new AISDKToolkit({ toolkit }).tools({ frontend })`. It is a strict superset (it also opens MCP server connections), so it replaces `generativeTools` everywhere. The `frontendTools` option is named `frontend` on `.tools()`, and `.tools()` is async. `generativeTools` will be removed in a future version.

Builds an AI SDK `ToolSet` for server-side use with `streamText` / `generateText` from a generative `toolkit` and the frontend-uploaded tools.

Each toolkit tool's `execute` runs on the server. Pair this with the `"use generative"` compiler: import the toolkit in a server route (where it resolves to the server build — schema + `execute`, with `render` stripped) and pass it here. Tools without an `execute` are still exposed to the model but left for the client to fulfill. `frontendTools` lets the client contribute tools that aren't in the static toolkit.

```
// Define once at module scope so any MCP connections pool across requests.
const aiToolkit = new AISDKToolkit({ toolkit: docsToolkit });

// In your route handler:
const { tools } = await req.json();
streamText({
  abortSignal: req.signal,
  model,
  messages,
  tools: await aiToolkit.tools({ frontend: tools }),
});
```

```
const generativeTools: (options: GenerativeToolsOptions) => ToolSet;
```