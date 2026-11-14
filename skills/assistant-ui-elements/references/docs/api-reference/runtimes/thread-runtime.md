# ThreadRuntime
URL: /docs/api-reference/runtimes/thread-runtime

ThreadRuntime state and actions for controlling assistant-ui messages, composers, suggestions, model context, and the full thread lifecycle.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### ThreadComposerRuntime

- `path`: `ComposerRuntimePath`

  - `ref`: `string`
  - `threadSelector`: `ThreadComposerRuntime["path"]["threadSelector"]`
    - `type`: `"main"`
  - `composerSource`: `"thread"`

- `type`: `"edit" | "thread"`

- `addAttachment`: `(fileOrAttachment: File | CreateAttachment) => Promise<void>` — Add an attachment to the composer. Accepts either a standard File object (processed through the AttachmentAdapter) or a CreateAttachment descriptor for external-source attachments (URLs, API data, CMS references). External descriptors bypass the adapter's \`add()\` step but still respect \`adapter.accept\` when an adapter is configured; without an adapter they are added as-is.

- `setText`: `(text: string, options?: ComposerSetTextOptions) => void` — Set the text of the composer.

- `setRole`: `(role: MessageRole) => void` — Set the role of the composer. For instance, if you'd like a specific message to have the 'assistant' role, you can do so here.

- `setRunConfig`: `(runConfig: RunConfig) => void` — Set the run config of the composer. This is used to send custom configuration data to the model. Within your backend, you can use the \`runConfig\` object. Example: \`\`\`ts composerRuntime.setRunConfig({ custom: { customField: "customValue" } }); \`\`\`

- `reset`: `() => Promise<void>` — Reset the composer. This will clear the entire state of the composer, including all text and attachments.

- `clearAttachments`: `() => Promise<void>` — Clear all attachments from the composer.

- `send`: `(options?: SendOptions) => void` — Send a message. This will send whatever text or attachments are in the composer.

- `cancel`: `() => void` — Cancel the current run. In edit mode, this will exit edit mode.

- `steerQueueItem`: `(queueItemId: string) => void` (deprecated: Use \`moveQueueItem(queueItemId, { lane: "steer", insertAfter: null })\` instead. Removal after 2026-11-05.)

- `moveQueueItem`: `(queueItemId: string, placement: QueuePlacement) => void` — Move a queued message between lanes or within a lane.

- `removeQueueItem`: `(queueItemId: string) => void` — Remove a queued message.

- `subscribe`: `(callback: () => void) => Unsubscribe` — Listens for changes to the composer state.

- `startDictation`: `() => void` — Start dictation to convert voice to text input. Requires a DictationAdapter to be configured.

- `stopDictation`: `() => void` — Stop the current dictation session.

- `setQuote`: `(quote: QuoteInfo | undefined) => void` — Set a quote for the next message. Pass undefined to clear.

- `unstable_on`: `<E extends ComposerRuntimeEventType>(event: E, callback: ComposerRuntimeEventCallback<E>) => Unsubscribe` (experimental)

- `getState`: `() => ThreadComposerState`

- `getAttachmentByIndex`: `(idx: number) => AttachmentRuntime & { source: "thread-composer"; }`

### ThreadComposerState

- `canCancel`: `boolean` — Whether the composer can cancel the current run. \`true\` when the runtime supports cancel and a run is in flight, not merely when cancel is a capability.

- `canSend`: `boolean`

- `isEditing`: `boolean`

- `isEmpty`: `boolean`

- `text`: `string`

- `role`: `MessageRole`

- `attachments`: `readonly Attachment[]`

- `runConfig`: `RunConfig`
  - `custom?`: `Record<string, unknown>`

- `attachmentAccept`: `string`

- `dictation?`: `DictationState | undefined` — The current state of dictation. Undefined when dictation is not active.

  - `status`: `DictationAdapter.Status`
    - `type`: `"starting" | "running"`
  - `transcript?`: `string`
  - `inputDisabled?`: `boolean`

- `quote?`: `QuoteInfo | undefined` — The currently quoted text, if any. Undefined when no quote is set.

  - `text`: `string`
  - `messageId`: `string`

- `queue`: `readonly QueueItemState[]` — Messages waiting to be processed. Empty unless the \`queue\` capability is set.

- `submission?`: `ComposerSubmission | undefined` — The message this composer sent while its attachments are prepared. Undefined once the runtime has taken it.

  - `id`: `string`

  - `role`: `MessageRole`

  - `text`: `string`

  - `quote?`: `QuoteInfo | undefined`

    - `text`: `string`
    - `messageId`: `string`

  - `attachments`: `readonly Attachment[]`

- `inTransit?`: `readonly ComposerSubmission[] | undefined` — Messages this composer handed to the runtime that the thread does not show yet, oldest first. The thread keeps rendering each one until the runtime shows the message it became.

- `type`: `"thread"`

### ThreadRuntime

- `path`: `ThreadRuntimePath` — The selector for the thread runtime.

  - `ref`: `string`
  - `threadSelector`: `ThreadRuntimePath["threadSelector"]`
    - `type`: `"main"`

- `composer`: `ThreadComposerRuntime` — The thread composer runtime.

  - `path`: `ComposerRuntimePath`

    - `ref`: `string`
    - `threadSelector`: `ThreadComposerRuntime["path"]["threadSelector"]`
      - `type`: `"main"`
    - `composerSource`: `"thread"`

  - `type`: `"edit" | "thread"`

  - `addAttachment`: `(fileOrAttachment: File | CreateAttachment) => Promise<void>` — Add an attachment to the composer. Accepts either a standard File object (processed through the AttachmentAdapter) or a CreateAttachment descriptor for external-source attachments (URLs, API data, CMS references). External descriptors bypass the adapter's \`add()\` step but still respect \`adapter.accept\` when an adapter is configured; without an adapter they are added as-is.

  - `setText`: `(text: string, options?: ComposerSetTextOptions) => void` — Set the text of the composer.

  - `setRole`: `(role: MessageRole) => void` — Set the role of the composer. For instance, if you'd like a specific message to have the 'assistant' role, you can do so here.

  - `setRunConfig`: `(runConfig: RunConfig) => void` — Set the run config of the composer. This is used to send custom configuration data to the model. Within your backend, you can use the \`runConfig\` object. Example: \`\`\`ts composerRuntime.setRunConfig({ custom: { customField: "customValue" } }); \`\`\`

  - `reset`: `() => Promise<void>` — Reset the composer. This will clear the entire state of the composer, including all text and attachments.

  - `clearAttachments`: `() => Promise<void>` — Clear all attachments from the composer.

  - `send`: `(options?: SendOptions) => void` — Send a message. This will send whatever text or attachments are in the composer.

  - `cancel`: `() => void` — Cancel the current run. In edit mode, this will exit edit mode.

  - `steerQueueItem`: `(queueItemId: string) => void` (deprecated: Use \`moveQueueItem(queueItemId, { lane: "steer", insertAfter: null })\` instead. Removal after 2026-11-05.)

  - `moveQueueItem`: `(queueItemId: string, placement: QueuePlacement) => void` — Move a queued message between lanes or within a lane.

  - `removeQueueItem`: `(queueItemId: string) => void` — Remove a queued message.

  - `subscribe`: `(callback: () => void) => Unsubscribe` — Listens for changes to the composer state.

  - `startDictation`: `() => void` — Start dictation to convert voice to text input. Requires a DictationAdapter to be configured.

  - `stopDictation`: `() => void` — Stop the current dictation session.

  - `setQuote`: `(quote: QuoteInfo | undefined) => void` — Set a quote for the next message. Pass undefined to clear.

  - `unstable_on`: `<E extends ComposerRuntimeEventType>(event: E, callback: ComposerRuntimeEventCallback<E>) => Unsubscribe` (experimental)

  - `getState`: `() => ThreadComposerState`

  - `getAttachmentByIndex`: `(idx: number) => AttachmentRuntime & { source: "thread-composer"; }`

- `getState`: `() => ThreadRuntimeState` — Gets a snapshot of the thread state.

- `append`: `(message: CreateAppendMessage) => void` — Append a new message to the thread.

- `deleteMessage`: `(messageId: string) => void | Promise<void>`

- `startRun`: `(config: CreateStartRunConfig) => void` — Start a new run with the given configuration.

- `resumeRun`: `(config: CreateResumeRunConfig) => void` — Resume a run with the given configuration.

- `exportExternalState`: `() => any` — Export the thread state in the external store format. For AI SDK runtimes, this returns the AI SDK message format. For other runtimes, this may return different formats or throw an error.

- `importExternalState`: `(state: any) => void` — Import thread state from the external store format. For AI SDK runtimes, this accepts AI SDK messages. For other runtimes, this may accept different formats or throw an error.

- `subscribe`: `(callback: () => void) => Unsubscribe`

- `cancelRun`: `() => void`

- `loadEarlier`: `() => Promise<void>` — Loads the page before the first loaded message; resolves at once when \`hasEarlier\` is false. Concurrent calls share one load.

- `unstable_notifySessionReset`: `() => void` (experimental) — Notifies the runtime that the adapter discarded its backing session. Clears session-scoped tool-invocation state without run-cancel side effects such as composer draft restoration. Internal API for external-store adapter authors.

- `getModelContext`: `() => ModelContext`

- `export`: `() => ExportedMessageRepository`

- `import`: `(repository: ExportedMessageRepository) => void`

- `reset`: `(initialMessages?: readonly ThreadMessageLike[]) => void` — Reset the thread with optional initial messages.

- `getMessageByIndex`: `(idx: number) => MessageRuntime`

- `getMessageById`: `(messageId: string) => MessageRuntime`

- `stopSpeaking`: `() => void` (experimental)

- `connectVoice`: `() => void`

- `disconnectVoice`: `() => void`

- `getVoiceVolume`: `() => number`

- `subscribeVoiceVolume`: `(callback: () => void) => Unsubscribe`

- `muteVoice`: `() => void`

- `unmuteVoice`: `() => void`

- `unstable_on`: `<E extends ThreadRuntimeEventType>(event: E, callback: ThreadRuntimeEventCallback<E>) => Unsubscribe` (experimental)

### ThreadRuntimeState

- `threadId`: `string` (deprecated: This field is deprecated and will be removed in 0.12.0. Use \`useThreadListItem().id\` instead.) — The thread ID.

- `metadata`: `ThreadListItemRuntimeState` (deprecated: Use \`useThreadListItem()\` instead. This field is deprecated and will be removed in 0.12.0.) — The thread metadata.

  - `isMain`: `boolean`
  - `isRunning`: `boolean` — Whether this thread has a run in progress, including a run that continues after the user switches to another thread.
  - `id`: `string`
  - `remoteId?`: `string | undefined`
  - `externalId?`: `string | undefined`
  - `status`: `ThreadListItemStatus`
  - `title?`: `string | undefined`
  - `lastMessageAt?`: `Date | undefined`
  - `custom?`: `Record<string, unknown> | undefined`

- `isDisabled`: `boolean` — Whether typing into the composer, adding attachments, sending, and editing messages are disabled for the thread.

- `isSendDisabled`: `boolean` — Whether the runtime's send policy disables composer sends, apart from whether the current draft is ready.

- `isLoading`: `boolean` — Whether the thread is loading its history.

- `hasEarlier`: `boolean` — Whether messages exist before the first loaded one, for a runtime that pages long threads.

- `isLoadingEarlier`: `boolean` — Whether the page before the first loaded message is being loaded.

- `isRunning`: `boolean` — Whether the thread is running. A thread is considered running when there is an active stream connection to the backend.

- `canResume`: `boolean` — Whether the adapter can resume the current interrupted run from its checkpoint.

- `capabilities`: `RuntimeCapabilities` — The capabilities of the thread, such as whether the thread supports editing, branch switching, etc.

  - `switchToBranch`: `boolean`
  - `switchBranchDuringRun`: `boolean`
  - `edit`: `boolean`
  - `reload`: `boolean`
  - `refetchThread`: `boolean` — Whether the runtime can refetch this thread's remote state in place.
  - `delete`: `boolean`
  - `cancel`: `boolean`
  - `unstable_copy`: `boolean` (experimental)
  - `speech`: `boolean`
  - `dictation`: `boolean`
  - `voice`: `boolean`
  - `attachments`: `boolean`
  - `feedback`: `boolean`
  - `queue`: `boolean`
  - `answerToolCall`: `boolean` — Whether the thread can answer a waiting tool call by adding its result, resuming it, or responding to its approval.

- `messages`: `readonly ThreadMessage[]` — The messages in the currently selected branch of the thread.

- `state`: `ReadonlyJSONValue` (experimental) — The thread state.

- `suggestions`: `readonly ThreadSuggestion[]` — Follow up message suggestions to show the user.

- `extras`: `unknown` — Custom extra information provided by the runtime.

- `speech?`: `SpeechState | undefined` (experimental)

  - `messageId`: `string`
  - `status`: `SpeechSynthesisAdapter.Status`
    - `type`: `"starting" | "running"`

- `voice?`: `VoiceSessionState | undefined`

  - `status`: `RealtimeVoiceAdapter.Status`
    - `type`: `"starting" | "running"`
  - `isMuted`: `boolean`
  - `mode`: `RealtimeVoiceAdapter.Mode`
  - `canSendText`: `boolean` — Whether the running session takes typed text. While true, \`append\` routes a plain text user message into the session and the thread composer can send.

### ThreadsState

- `canUnarchive`: `boolean` — Whether the thread list supports restoring archived threads.

- `mainThreadId`: `string`

- `newThreadId`: `string | null`

- `isLoading`: `boolean`

- `loadError`: `unknown`

- `switchError?`: `ThreadsState["switchError"]` — The most recent thread switch that failed: the id it was asked to open and the error it threw. Cleared when a later switch starts or that thread opens. A switch to a controlled \`threadId\` is retried when the browser comes back online or the tab becomes visible.

  - `threadId`: `string`
  - `error`: `unknown`

- `isLoadingMore`: `boolean`

- `hasMore`: `boolean`

- `threadIds`: `readonly string[]`

- `archivedThreadIds`: `readonly string[]`

- `threadItems`: `readonly ThreadListItemState[]`

- `main`: `ThreadState`

  - `isEmpty`: `boolean` — Whether the thread is empty. A thread is considered empty when it has no messages and is not loading.

  - `isDisabled`: `boolean` — Whether typing into the composer, adding attachments, sending, and editing messages are disabled for the thread.

  - `isSendDisabled`: `boolean` — Whether the runtime's send policy disables composer sends, apart from whether the current draft is ready.

  - `isLoading`: `boolean` — Whether the thread is loading its history.

  - `hasEarlier`: `boolean` — Whether messages exist before the first loaded one, for a runtime that pages long threads.

  - `isLoadingEarlier`: `boolean` — Whether the page before the first loaded message is being loaded.

  - `isRunning`: `boolean` — Whether the thread is running. A thread is considered running when there is an active stream connection to the backend.

  - `canResume?`: `boolean | undefined` — Whether the adapter can resume the current interrupted run from its checkpoint.

  - `capabilities`: `RuntimeCapabilities` — The capabilities of the thread, such as whether the thread supports editing, branch switching, etc.

    - `switchToBranch`: `boolean`
    - `switchBranchDuringRun`: `boolean`
    - `edit`: `boolean`
    - `reload`: `boolean`
    - `refetchThread`: `boolean` — Whether the runtime can refetch this thread's remote state in place.
    - `delete`: `boolean`
    - `cancel`: `boolean`
    - `unstable_copy`: `boolean` (experimental)
    - `speech`: `boolean`
    - `dictation`: `boolean`
    - `voice`: `boolean`
    - `attachments`: `boolean`
    - `feedback`: `boolean`
    - `queue`: `boolean`
    - `answerToolCall`: `boolean` — Whether the thread can answer a waiting tool call by adding its result, resuming it, or responding to its approval.

  - `messages`: `readonly MessageState[]` — The messages in the currently selected branch of the thread.

  - `tasks`: `readonly TaskState[]` — Child work derived from the thread's tool calls: every tool call that carries a nested conversation, in document order with nested tasks after their parent. The array keeps its identity while no task changed.

  - `state`: `ReadonlyJSONValue` (experimental) — The thread state.

  - `suggestions`: `readonly ThreadSuggestion[]` — Follow up message suggestions to show the user.

  - `extras`: `unknown` — Custom extra information provided by the runtime.

  - `speech?`: `SpeechState | undefined` (experimental)

    - `messageId`: `string`
    - `status`: `SpeechSynthesisAdapter.Status`
      - `type`: `"starting" | "running"`

  - `voice?`: `VoiceSessionState | undefined`

    - `status`: `RealtimeVoiceAdapter.Status`
      - `type`: `"starting" | "running"`
    - `isMuted`: `boolean`
    - `mode`: `RealtimeVoiceAdapter.Mode`
    - `canSendText`: `boolean` — Whether the running session takes typed text. While true, \`append\` routes a plain text user message into the session and the thread composer can send.

  - `composer`: `ComposerState`

    - `text`: `string`

    - `role`: `MessageRole`

    - `attachments`: `readonly Attachment[]`

    - `runConfig`: `RunConfig`
      - `custom?`: `Record<string, unknown>`

    - `isEditing`: `boolean`

    - `canCancel`: `boolean` — Whether the composer can cancel the current run. \`true\` when the runtime supports cancel and a run is in flight, not merely when cancel is a capability.

    - `canSend`: `boolean` — Whether the composer is currently willing to send. \`true\` when the composer is in editing mode and has non-empty content; for thread composers require the thread to be enabled and its \`isSendDisabled\` flag to be unset, and, while a voice session is connected, a session that takes typed text (\`voice.canSendText\`) and a draft without attachments. Edit composers (saving message edits) also require the thread to be enabled, ignore \`isSendDisabled\` since it is a thread-scoped gate, and never send while a voice session is connected. Cross-thread gating (running, queue capability) is layered on top by \`useComposerSend\`.

    - `attachmentAccept`: `string`

    - `isEmpty`: `boolean`

    - `type`: `"thread" | "edit"`

    - `dictation?`: `DictationState | undefined` — The current state of dictation. Undefined when dictation is not active.

      - `status`: `DictationAdapter.Status`
      - `transcript?`: `string`
      - `inputDisabled?`: `boolean`

    - `quote?`: `QuoteInfo | undefined` — The currently quoted text, if any. Undefined when no quote is set.

      - `text`: `string`
      - `messageId`: `string`

    - `queue`: `readonly QueueItemState[]` — The queue of messages waiting to be processed. Empty when no messages are queued.

    - `submission?`: `ComposerSubmission | undefined` — The message this composer sent while its attachments are prepared. The thread renders it as its last message until the runtime takes it. Undefined when no send is being prepared.

      - `id`: `string`
      - `role`: `MessageRole`
      - `text`: `string`
      - `quote?`: `QuoteInfo | undefined`
      - `attachments`: `readonly Attachment[]`

    - `inTransit?`: `readonly ComposerSubmission[] | undefined` — Messages this composer handed to the runtime that the thread does not show yet, oldest first. The thread renders them after its own messages until the runtime shows each one.

### ThreadViewportState

- `isAtBottom`: `boolean`

- `scrollToBottom`: `(config?: { behavior?: ScrollBehavior | undefined; }) => void`

- `onScrollToBottom`: `( callback: ({ behavior }: { behavior: ScrollBehavior }) => void, ) => Unsubscribe`

- `turnAnchor`: `"top" | "bottom"` — Controls scroll anchoring: "top" anchors user messages at top, "bottom" is classic behavior

- `topAnchorMessageClamp`: `ThreadViewportState["topAnchorMessageClamp"]` — Clamps tall user messages so the assistant response stays in view.

  - `tallerThan`: `string`
  - `visibleHeight`: `string`

- `height`: `ThreadViewportState["height"]` — Raw height values from registered elements

  - `viewport`: `number` — Total viewport height
  - `inset`: `number` — Total content inset height (footer, anchor message, etc.)

- `element`: `ThreadViewportState["element"]` — Current DOM elements used for geometry-based top anchoring

  - `viewport`: `HTMLElement | null`
  - `anchor`: `HTMLElement | null`
  - `target`: `HTMLElement | null`

- `targetConfig`: `ThreadViewportState["targetConfig"]` — Numeric clamp configuration for the active top-anchor target message

  - `tallerThan`: `number`
  - `visibleHeight`: `number`

- `topAnchorTurn`: `ThreadViewportState["topAnchorTurn"]` — The current top-anchor turn activated in this viewport session. History-loaded messages do not populate this; it is set when a run creates a live user/assistant pair and remains after the run completes.

  - `anchorId`: `string`
  - `targetId`: `string`

- `registerViewport`: `() => SizeHandle` — Register a viewport and get a handle to update its height

- `registerContentInset`: `() => SizeHandle` — Register a content inset (footer, anchor message, etc.) and get a handle to update its height

- `registerViewportElement`: `( element: HTMLElement | null, ) => Unsubscribe` — Register the scroll viewport element

- `registerAnchorElement`: `(element: HTMLElement | null) => Unsubscribe` — Register the current anchor user message element

- `registerAnchorTargetElement`: `( element: HTMLElement | null, config?: { readonly tallerThan: number; readonly visibleHeight: number }, ) => Unsubscribe` — Register the current top-anchor target (last assistant response) element along with its numeric clamp configuration. When unregistered, both \`element.target\` and \`targetConfig\` clear together.

- `setTopAnchorTurn`: `( turn: { readonly anchorId: string; readonly targetId: string } | null, ) => void`

### ThreadState

> [!warn]
>
> **Deprecated.** Use `ThreadRuntimeState`. From `@assistant-ui/react` 0.16, `ThreadState` names the thread state read through `useAuiState`.

- `threadId`: `string` (deprecated: This field is deprecated and will be removed in 0.12.0. Use \`useThreadListItem().id\` instead.) — The thread ID.

- `metadata`: `ThreadListItemRuntimeState` (deprecated: Use \`useThreadListItem()\` instead. This field is deprecated and will be removed in 0.12.0.) — The thread metadata.

  - `isMain`: `boolean`
  - `isRunning`: `boolean` — Whether this thread has a run in progress, including a run that continues after the user switches to another thread.
  - `id`: `string`
  - `remoteId?`: `string | undefined`
  - `externalId?`: `string | undefined`
  - `status`: `ThreadListItemStatus`
  - `title?`: `string | undefined`
  - `lastMessageAt?`: `Date | undefined`
  - `custom?`: `Record<string, unknown> | undefined`

- `isDisabled`: `boolean` — Whether typing into the composer, adding attachments, sending, and editing messages are disabled for the thread.

- `isSendDisabled`: `boolean` — Whether the runtime's send policy disables composer sends, apart from whether the current draft is ready.

- `isLoading`: `boolean` — Whether the thread is loading its history.

- `hasEarlier`: `boolean` — Whether messages exist before the first loaded one, for a runtime that pages long threads.

- `isLoadingEarlier`: `boolean` — Whether the page before the first loaded message is being loaded.

- `isRunning`: `boolean` — Whether the thread is running. A thread is considered running when there is an active stream connection to the backend.

- `canResume`: `boolean` — Whether the adapter can resume the current interrupted run from its checkpoint.

- `capabilities`: `RuntimeCapabilities` — The capabilities of the thread, such as whether the thread supports editing, branch switching, etc.

  - `switchToBranch`: `boolean`
  - `switchBranchDuringRun`: `boolean`
  - `edit`: `boolean`
  - `reload`: `boolean`
  - `refetchThread`: `boolean` — Whether the runtime can refetch this thread's remote state in place.
  - `delete`: `boolean`
  - `cancel`: `boolean`
  - `unstable_copy`: `boolean` (experimental)
  - `speech`: `boolean`
  - `dictation`: `boolean`
  - `voice`: `boolean`
  - `attachments`: `boolean`
  - `feedback`: `boolean`
  - `queue`: `boolean`
  - `answerToolCall`: `boolean` — Whether the thread can answer a waiting tool call by adding its result, resuming it, or responding to its approval.

- `messages`: `readonly ThreadMessage[]` — The messages in the currently selected branch of the thread.

- `state`: `ReadonlyJSONValue` (experimental) — The thread state.

- `suggestions`: `readonly ThreadSuggestion[]` — Follow up message suggestions to show the user.

- `extras`: `unknown` — Custom extra information provided by the runtime.

- `speech?`: `SpeechState | undefined` (experimental)

  - `messageId`: `string`
  - `status`: `SpeechSynthesisAdapter.Status`
    - `type`: `"starting" | "running"`

- `voice?`: `VoiceSessionState | undefined`

  - `status`: `RealtimeVoiceAdapter.Status`
    - `type`: `"starting" | "running"`
  - `isMuted`: `boolean`
  - `mode`: `RealtimeVoiceAdapter.Mode`
  - `canSendText`: `boolean` — Whether the running session takes typed text. While true, \`append\` routes a plain text user message into the session and the thread composer can send.