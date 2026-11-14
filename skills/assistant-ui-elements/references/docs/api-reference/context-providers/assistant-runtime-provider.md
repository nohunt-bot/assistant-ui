# AssistantRuntimeProvider
URL: /docs/api-reference/context-providers/assistant-runtime-provider

Root React provider that connects an assistant-ui runtime to primitives, hooks, threads, and composer state.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### AssistantRuntimeProvider

```
import { AssistantRuntimeProvider } from "@assistant-ui/react";
import { useChatRuntime, AssistantChatTransport } from "@assistant-ui/ai-sdk";

const MyApp = () => {
  const runtime = useChatRuntime({
    transport: new AssistantChatTransport({
      api: "/api/chat",
    }),
  });

  return (
    <AssistantRuntimeProvider runtime={runtime}>
      {/* your app */}
    </AssistantRuntimeProvider>
  );
};
```

- `runtime`: `AssistantRuntime` — The assistant runtime to expose to descendants. Build one with \`useLocalRuntime\`, \`useExternalStoreRuntime\`, or \`useAssistantTransportRuntime\`.

  - `threads`: `ThreadListRuntime` — The threads in this assistant.

    - `getState`: `() => ThreadListState`

    - `subscribe`: `(callback: () => void) => Unsubscribe`

    - `main`: `ThreadRuntime`

      - `path`: `ThreadRuntimePath` — The selector for the thread runtime.
      - `composer`: `ThreadComposerRuntime` — The thread composer runtime.
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

    - `getById`: `(threadId: string) => ThreadRuntime`

    - `mainItem`: `ThreadListItemRuntime`

      - `path`: `ThreadListItemRuntimePath`
      - `getState`: `() => ThreadListItemRuntimeState`
      - `initialize`: `() => Promise<{ remoteId: string; externalId: string | undefined; }>`
      - `generateTitle`: `(options?: ThreadListItemGenerateTitleOptions) => Promise<void>`
      - `switchTo`: `(options?: { unarchive?: boolean; }) => Promise<void>`
      - `rename`: `(newTitle: string) => Promise<void>`
      - `updateCustom`: `(custom: Record<string, unknown> | undefined) => Promise<void>`
      - `archive`: `() => Promise<void>`
      - `unarchive`: `() => Promise<void>`
      - `delete`: `() => Promise<void>`
      - `detach`: `() => void`
      - `subscribe`: `(callback: () => void) => Unsubscribe`
      - `unstable_on`: `<E extends ThreadListItemEventType>(event: E, callback: ThreadListItemEventCallback<E>) => Unsubscribe` (experimental)

    - `getItemById`: `(threadId: string) => ThreadListItemRuntime`

    - `getItemByIndex`: `(idx: number) => ThreadListItemRuntime`

    - `getArchivedItemByIndex`: `(idx: number) => ThreadListItemRuntime`

    - `switchToThread`: `(threadId: string, options?: { unarchive?: boolean; }) => Promise<void>`

    - `switchToNewThread`: `() => Promise<void>`

    - `unstable_subscribeThreadEvents`: `(callback: (event: ThreadListRuntimeEvent) => void) => Unsubscribe` (experimental) — Observes lifecycle events on every thread this list keeps alive, so an event that fires while its thread is not selected stays observable. Thread lists that mount only the main thread never emit; their main thread's runtime is observed directly.

    - `getLoadThreadsPromise`: `() => Promise<void>`

    - `reload`: `() => Promise<void>`

    - `reloadMainThread`: `() => Promise<void>` — Refetches the open thread's remote state, for state that changed out of band and so never reached the stream. When the runtime declares the in-place capability (\`unstable_refetchThread\`), composer drafts survive, existing messages stay rendered during the refetch, and the promise settles with the refetch, rejecting if it fails; that runtime also owns what happens to a run in progress, since this does not stop one. Runtimes without the capability have their hook remounted instead, which discards unsent composer input and ends any run, and the promise resolves once the new runtime attaches. A thread that has not been sent yet is left alone.

    - `loadMore`: `() => Promise<void>`

  - `thread`: `ThreadRuntime` — The currently selected main thread. Equivalent to \`threads.main\`.

    - `path`: `ThreadRuntimePath` — The selector for the thread runtime.

      - `ref`: `string`
      - `threadSelector`: `{ readonly type: "main" } | { readonly type: "threadId"; readonly threadId: string; }`

    - `composer`: `ThreadComposerRuntime` — The thread composer runtime.

      - `path`: `ComposerRuntimePath`
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

  - `registerModelContextProvider`: `(provider: ModelContextProvider) => Unsubscribe` — Register a model context provider. Model context providers are configuration such as system message, temperature, etc. that are set in the frontend.

- `aui?`: `AssistantClient` — Optional parent \`AssistantClient\` whose scopes are inherited by the client created for this runtime. Use this when nesting an \`AssistantRuntimeProvider\` inside another assistant context. Omit this prop when there is no parent client.

  - `thread`: `AssistantClientAccessor<"thread">`

  - `message`: `AssistantClientAccessor<"message">`

  - `threads`: `AssistantClientAccessor<"threads">`

  - `threadListItem`: `AssistantClientAccessor<"threadListItem">`

  - `part`: `AssistantClientAccessor<"part">`

  - `composer`: `AssistantClientAccessor<"composer">`

  - `attachment`: `AssistantClientAccessor<"attachment">`

  - `modelContext`: `AssistantClientAccessor<"modelContext">`

  - `suggestions`: `AssistantClientAccessor<"suggestions">`

  - `suggestion`: `AssistantClientAccessor<"suggestion">`

  - `chainOfThought`: `AssistantClientAccessor<"chainOfThought">`

  - `queueItem`: `AssistantClientAccessor<"queueItem">`

  - `task`: `AssistantClientAccessor<"task">`

  - `tools`: `AssistantClientAccessor<"tools">`

  - `dataRenderers`: `AssistantClientAccessor<"dataRenderers">`

  - `interactables`: `AssistantClientAccessor<"interactables">`

  - `unstable_interactables`: `AssistantClientAccessor<"unstable_interactables">`

  - `optional`: `AssistantClient["optional"]`

    - `thread?`: `AssistantClientAccessor<"thread">`

      - `getState`: `() => ThreadState` — Get the current state of the thread.
      - `composer`: `() => ComposerMethods` — The thread composer runtime.
      - `suggestions`: `() => SuggestionsMethods` — The suggestions shown for this thread.
      - `task`: `(selector: { index: number; } | { id: string; }) => TaskMethods` — Access a task by index or toolCallId; an id resolves the first task with that toolCallId in document order.
      - `append`: `(message: CreateAppendMessage) => void` — Append a new message to the thread.
      - `deleteMessage`: `(messageId: string) => void | Promise<void>`
      - `startRun`: `(config: CreateStartRunConfig) => void` — Start a new run with the given configuration.
      - `resumeRun`: `(config: CreateResumeRunConfig) => void` — Resume a run with the given configuration.
      - `cancelRun`: `() => void`
      - `loadEarlier`: `() => Promise<void>` — Load the page before the first loaded message. Resolves at once when \`hasEarlier\` is false; concurrent calls share one load, and a failed load is logged rather than rejected.
      - `unstable_refetchThread?`: `() => Promise<void>` (experimental) — Re-fetch this thread's state from its backing store, in place: the tap thread's refetch hook, which \`threads.reloadMainThread()\` prefers and whose rejection it propagates. \`capabilities.refetchThread\` is the portable feature-detection signal; a legacy-bridged thread reports it there while routing the refetch through its runtime, not this method. The method-shorthand optionality is load-bearing: an explicit \`| undefined\` stops \`ThreadMethods\` satisfying \`ClientMethods\` and collapses the client schema, which only a workspace-level app typecheck surfaces.
      - `getModelContext`: `() => ModelContext`
      - `export`: `() => ExportedMessageRepository`
      - `import`: `(repository: ExportedMessageRepository) => void`
      - `reset`: `(initialMessages?: readonly ThreadMessageLike[]) => void` — Reset the thread with optional initial messages.
      - `importExternalState`: `(state: unknown) => void`
      - `message`: `(selector: { id: string; } | { index: number; }) => MessageMethods`
      - `stopSpeaking`: `() => void` (experimental)
      - `connectVoice`: `() => void`
      - `disconnectVoice`: `() => void`
      - `getVoiceVolume`: `() => number`
      - `subscribeVoiceVolume`: `(callback: () => void) => Unsubscribe`
      - `muteVoice`: `() => void`
      - `unmuteVoice`: `() => void`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `message?`: `AssistantClientAccessor<"message">`

      - `getState`: `() => MessageState` — Get the current state of the message.
      - `composer`: `() => ComposerMethods`
      - `delete`: `() => void | Promise<void>`
      - `reload`: `(config?: { runConfig?: RunConfig; }) => void`
      - `speak`: `() => void` (experimental)
      - `stopSpeaking`: `() => void` (experimental)
      - `submitFeedback`: `(feedback: { type: "positive" | "negative"; comment?: string; }) => void`
      - `switchToBranch`: `(options: { position?: "previous" | "next"; branchId?: string; }) => void`
      - `getCopyText`: `() => string`
      - `part`: `(selector: { index: number; } | { toolCallId: string; }) => PartMethods`
      - `attachment`: `(selector: { index: number; } | { id: string; }) => AttachmentMethods`
      - `setIsCopied`: `(value: boolean) => void`
      - `setIsHovering`: `(value: boolean) => void`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `threads?`: `AssistantClientAccessor<"threads">`

      - `getState`: `() => ThreadsState`
      - `switchToThread`: `(threadId: string, options?: { unarchive?: boolean; }) => void`
      - `switchToNewThread`: `() => void`
      - `item`: `(threadIdOrOptions: "main" | { id: string; } | { index: number; archived?: boolean; }) => ThreadListItemMethods`
      - `thread`: `(selector: "main") => ThreadMethods`
      - `getLoadThreadsPromise`: `() => Promise<void>` — Resolves once \`getState()\` reports the loaded thread list, or its \`loadError\` when loading fails. If the client cannot commit within 100ms, as inside the React \`act()\` scope that completes the load or under a Suspense boundary that hides the client, it resolves anyway and \`getState()\` may not report the list yet.
      - `reload`: `() => Promise<void>` — Refetches the thread list; resolves like \`getLoadThreadsPromise()\`.
      - `reloadMainThread`: `() => Promise<void>`
      - `loadMore`: `() => Promise<void>` — Loads the next page; resolves once \`getState()\` reports it, or after a failed request, which is logged. If the client cannot commit within 100ms, as inside the React \`act()\` scope that completes the load or under a Suspense boundary that hides the client, it resolves anyway and \`getState()\` may not report the list yet.
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `threadListItem?`: `AssistantClientAccessor<"threadListItem">`

      - `getState`: `() => ThreadListItemState`
      - `switchTo`: `(options?: { unarchive?: boolean; }) => void`
      - `rename`: `(newTitle: string) => void`
      - `updateCustom`: `(custom: Record<string, unknown> | undefined) => void`
      - `archive`: `() => void`
      - `unarchive`: `() => void`
      - `delete`: `() => void`
      - `generateTitle`: `(options?: ThreadListItemGenerateTitleOptions) => void`
      - `initialize`: `() => Promise<{ remoteId: string; externalId: string | undefined; }>`
      - `detach`: `() => void`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `part?`: `AssistantClientAccessor<"part">`

      - `getState`: `() => PartState` — Get the current state of the message part.
      - `addToolResult`: `(result: unknown | ToolResponse<unknown>) => void` — Add tool result to a tool call message part that has no tool result yet. This is useful when you are collecting a tool result via user input ("human tool calls").
      - `resumeToolCall`: `(payload: unknown) => void` — Resume a tool call that is waiting for human input with a payload. This is useful when a tool has requested human input and is waiting for a response.
      - `respondToToolApproval`: `(response: ToolApprovalResponse) => Promise<void>` — Respond to a server-side tool approval gate. The approval id is read from the part. Accepts a boolean decision, the id of one of the approval's options, or a free-form answer when the request asks for one. Resolves once the runtime has accepted the response, and rejects when it could not be recorded, so a renderer can leave its controls retryable.
      - `unstable_recordInteraction?`: `(input: Unstable_ToolInteractionInput) => Promise<void>` (experimental) — Record a user interaction on a tool call part. Resolves once recorded and rejects when it cannot be.
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `composer?`: `AssistantClientAccessor<"composer">`

      - `getState`: `() => ComposerState`
      - `setText`: `(text: string, options?: ComposerSetTextOptions) => void`
      - `setRole`: `(role: MessageRole) => void`
      - `setRunConfig`: `(runConfig: RunConfig) => void`
      - `addAttachment`: `(fileOrAttachment: File | CreateAttachment) => Promise<void>`
      - `clearAttachments`: `() => Promise<void>`
      - `attachment`: `(selector: { index: number; } | { id: string; }) => AttachmentMethods`
      - `reset`: `() => Promise<void>`
      - `send`: `(opts?: ComposerSendOptions) => void`
      - `cancel`: `() => void`
      - `beginEdit`: `() => void`
      - `startDictation`: `() => void` — Start dictation to convert voice to text input. Requires a DictationAdapter to be configured.
      - `stopDictation`: `() => void` — Stop the current dictation session.
      - `setQuote`: `(quote: QuoteInfo | undefined) => void` — Set a quote for the next message. Pass undefined to clear.
      - `queueItem`: `(selector: { index: number; } | { id: string; }) => QueueItemMethods` — Access a queue item by index or id.
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `attachment?`: `AssistantClientAccessor<"attachment">`

      - `getState`: `() => AttachmentState`
      - `remove`: `() => Promise<void>`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `modelContext?`: `AssistantClientAccessor<"modelContext">`

      - `getModelContext`: `() => ModelContext`
      - `subscribe?`: `(callback: () => void) => Unsubscribe`
      - `getState`: `() => ModelContextState`
      - `register`: `(provider: ModelContextProvider) => Unsubscribe`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `suggestions?`: `AssistantClientAccessor<"suggestions">`

      - `getState`: `() => SuggestionsState`
      - `suggestion`: `(query: { index: number; }) => SuggestionMethods`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `suggestion?`: `AssistantClientAccessor<"suggestion">`

      - `getState`: `() => SuggestionState`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `chainOfThought?`: `AssistantClientAccessor<"chainOfThought">`

      - `getState`: `() => ChainOfThoughtState` — Get the current state of the chain of thought.
      - `setCollapsed`: `(collapsed: boolean) => void` — Set the collapsed state of the chain of thought accordion.
      - `part`: `(selector: { index: number; }) => PartMethods` — Get the part methods for a specific part within this chain of thought.
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `queueItem?`: `AssistantClientAccessor<"queueItem">`

      - `getState`: `() => QueueItemState`
      - `steer`: `() => void` (deprecated: Use \`move({ lane: "steer", insertAfter: null })\` instead. Removal after 2026-11-05.)
      - `move`: `(placement: QueuePlacement) => void`
      - `remove`: `() => void`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `task?`: `AssistantClientAccessor<"task">`

      - `getState`: `() => TaskState`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `tools?`: `AssistantClientAccessor<"tools">`

      - `getState`: `() => ToolsState`
      - `setToolUI`: `(toolName: string, render: ToolCallMessagePartComponent, options?: { standalone?: boolean; }) => Unsubscribe`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `dataRenderers?`: `AssistantClientAccessor<"dataRenderers">`

      - `getState`: `() => DataRenderersState`
      - `setDataUI`: `(name: string, render: DataMessagePartComponent) => Unsubscribe`
      - `setFallbackDataUI`: `(render: DataMessagePartComponent) => Unsubscribe`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `interactables?`: `AssistantClientAccessor<"interactables">`

      - `getState`: `() => InteractablesState`
      - `register`: `(def: InteractableRegistration) => Unsubscribe`
      - `setState`: `(id: string, updater: (prev: unknown) => unknown) => void`
      - `setSelected`: `(id: string, selected: boolean) => void`
      - `exportState`: `() => InteractablePersistedState`
      - `importState`: `(saved: InteractablePersistedState) => void`
      - `setPersistenceAdapter`: `(adapter: InteractablePersistenceAdapter | undefined) => void`
      - `flush`: `() => Promise<void>`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

    - `unstable_interactables?`: `AssistantClientAccessor<"unstable_interactables">`

      - `getState`: `() => Unstable_InteractablesState`
      - `register`: `(def: Unstable_InteractableRegistration) => Unsubscribe`
      - `setState`: `(id: string, updater: (prev: unknown) => unknown) => void`
      - `exportState`: `() => Unstable_InteractablePersistedState`
      - `importState`: `(saved: Unstable_InteractablePersistedState) => void`
      - `setPersistenceAdapter`: `(adapter: Unstable_InteractablePersistenceAdapter | undefined) => void`
      - `flush`: `() => Promise<void>`
      - `source`: `"root"`
      - `query`: `Record<string, never>`
      - `name`: `K`

  - `subscribe`: `(listener: () => void) => Unsubscribe`

  - `on`: `<TEvent extends AssistantEventName>(selector: AssistantEventSelector<TEvent>, callback: AssistantEventCallback<TEvent>) => Unsubscribe`

- `config?`: `AuiConfig` — Optional extra scopes provided alongside the runtime's \`threads\` scope; build with \`AuiConfig\`.

  - `thread?`: `ClientElement<"thread"> | DerivedElement<"thread">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `message?`: `ClientElement<"message"> | DerivedElement<"message">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `threads?`: `ClientElement<"threads"> | DerivedElement<"threads">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `threadListItem?`: `ClientElement<"threadListItem"> | DerivedElement<"threadListItem">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `part?`: `ClientElement<"part"> | DerivedElement<"part">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `composer?`: `ClientElement<"composer"> | DerivedElement<"composer">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `attachment?`: `ClientElement<"attachment"> | DerivedElement<"attachment">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `modelContext?`: `ClientElement<"modelContext"> | DerivedElement<"modelContext">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `suggestions?`: `ClientElement<"suggestions"> | DerivedElement<"suggestions">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `suggestion?`: `ClientElement<"suggestion"> | DerivedElement<"suggestion">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `chainOfThought?`: `ClientElement<"chainOfThought"> | DerivedElement<"chainOfThought">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `queueItem?`: `ClientElement<"queueItem"> | DerivedElement<"queueItem">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `task?`: `ClientElement<"task"> | DerivedElement<"task">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `tools?`: `ClientElement<"tools"> | DerivedElement<"tools">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `dataRenderers?`: `ClientElement<"dataRenderers"> | DerivedElement<"dataRenderers">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `interactables?`: `ClientElement<"interactables"> | DerivedElement<"interactables">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `unstable_interactables?`: `ClientElement<"unstable_interactables"> | DerivedElement<"unstable_interactables">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

### AuiProvider

Supplies an `AssistantClient` to the React tree.

Place near the root of any subtree that uses [useAui](/docs/api-reference/hooks/state#useaui) or the primitives built on it. Components rendered outside an `AuiProvider` receive a default client whose scope accessors throw on use, so missing-provider mistakes surface at the point of use.

`config` is required and must be built with [AuiConfig](/docs/api-reference/utilities/miscellaneous#auiconfig). At the top level, `config` alone creates this subtree's own client. Under a parent provider, `extends` is mandatory: pass `extends={aui}` to extend the parent client or `extends={null}` to isolate from it (enforced with a dev error). Configs are identity-insensitive — a fresh object per render is safe. A config whose scopes are all Derived keeps its scope set fixed at mount (dev-enforced); configs with a root scope, and empty configs, may grow and shrink scopes across renders. `ref` receives the resulting client after mount.

When mounting a runtime built with one of the runtime hooks, use [AssistantRuntimeProvider](/docs/api-reference/context-providers/assistant-runtime-provider#assistantruntimeprovider) — it installs an `AuiProvider` internally — rather than wiring `AuiProvider` yourself.

```
function MessageScope({ index, children }) {
  const aui = useAui();
  const config = AuiConfig({
    message: Derived({
      source: "thread",
      query: { index },
      get: (aui) => aui.thread.message({ index }),
    }),
  });
  return (
    <AuiProvider extends={aui} config={config}>
      {children}
    </AuiProvider>
  );
}
```

- `config`: `AuiConfig` — Scopes to create the client from; built with [AuiConfig](/docs/api-reference/utilities/miscellaneous#auiconfig).

  - `thread?`: `ClientElement<"thread"> | DerivedElement<"thread">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `message?`: `ClientElement<"message"> | DerivedElement<"message">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `threads?`: `ClientElement<"threads"> | DerivedElement<"threads">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `threadListItem?`: `ClientElement<"threadListItem"> | DerivedElement<"threadListItem">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `part?`: `ClientElement<"part"> | DerivedElement<"part">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `composer?`: `ClientElement<"composer"> | DerivedElement<"composer">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `attachment?`: `ClientElement<"attachment"> | DerivedElement<"attachment">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `modelContext?`: `ClientElement<"modelContext"> | DerivedElement<"modelContext">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `suggestions?`: `ClientElement<"suggestions"> | DerivedElement<"suggestions">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `suggestion?`: `ClientElement<"suggestion"> | DerivedElement<"suggestion">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `chainOfThought?`: `ClientElement<"chainOfThought"> | DerivedElement<"chainOfThought">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `queueItem?`: `ClientElement<"queueItem"> | DerivedElement<"queueItem">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `task?`: `ClientElement<"task"> | DerivedElement<"task">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `tools?`: `ClientElement<"tools"> | DerivedElement<"tools">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `dataRenderers?`: `ClientElement<"dataRenderers"> | DerivedElement<"dataRenderers">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `interactables?`: `ClientElement<"interactables"> | DerivedElement<"interactables">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

  - `unstable_interactables?`: `ClientElement<"unstable_interactables"> | DerivedElement<"unstable_interactables">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

- `ref?`: `React.Ref<AssistantClient>` — Receives the resulting client after mount.

- `extends?`: `never`

- `value?`: `never`

- `children?`: `React.ReactNode` — Subtree that may read from the client.