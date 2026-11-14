# ThreadListRuntime
URL: /docs/api-reference/runtimes/thread-list-runtime

ThreadListRuntime state and actions for managing remote assistant-ui conversations, active thread selection, and new thread creation.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### ThreadListRuntime

- `getState`: `() => ThreadListState`

- `subscribe`: `(callback: () => void) => Unsubscribe`

- `main`: `ThreadRuntime`

  - `path`: `ThreadRuntimePath` — The selector for the thread runtime.

    - `ref`: `string`
    - `threadSelector`: `ThreadRuntimePath["threadSelector"]`
      - `type`: `"main"`

  - `composer`: `ThreadComposerRuntime` — The thread composer runtime.

    - `path`: `ComposerRuntimePath`

      - `ref`: `string`
      - `threadSelector`: `{ readonly type: "main" } | { readonly type: "threadId"; readonly threadId: string; }`
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

- `getById`: `(threadId: string) => ThreadRuntime`

- `mainItem`: `ThreadListItemRuntime`

  - `path`: `ThreadListItemRuntimePath`

    - `ref`: `string`
    - `threadSelector`: `ThreadListItemRuntimePath["threadSelector"]`
      - `type`: `"main"`

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

### ThreadListState

- `canUnarchive`: `boolean` — Whether the thread list supports restoring archived threads.

- `mainThreadId`: `string`

- `newThreadId?`: `string | undefined`

- `threadIds`: `readonly string[]`

- `archivedThreadIds`: `readonly string[]`

- `isLoading`: `boolean`

- `loadError`: `unknown` — The error thrown by the most recent thread list load that failed, cleared when a later load starts.

- `switchError?`: `ThreadListState["switchError"]` — The most recent thread switch that failed: the id it was asked to open and the error it threw. Cleared when a later switch starts or that thread opens. A switch to a controlled \`threadId\` is retried when the browser comes back online or the tab becomes visible.

  - `threadId`: `string`
  - `error`: `unknown`

- `isLoadingMore`: `boolean`

- `hasMore`: `boolean`

- `threadItems`: `Readonly< Record< string, Omit<ThreadListItemRuntimeState, "isMain" | "threadId" | "isRunning"> > >`