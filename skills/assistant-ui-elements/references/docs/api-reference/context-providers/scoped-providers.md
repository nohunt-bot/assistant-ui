# Scoped Providers
URL: /docs/api-reference/context-providers/scoped-providers

Lower-level assistant-ui providers for custom renderers, scoped message parts, attachments, and advanced composition.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### ChainOfThoughtByIndicesProvider

- `startIndex`: `number`
- `endIndex`: `number`

### ChainOfThoughtPartByIndexProvider

- `index`: `number`

### ComposerAttachmentByIndexProvider

- `index`: `number`

### MessageAttachmentByIndexProvider

- `index`: `number`

### MessageByIndexProvider

- `index`: `number`

### MessageProvider

- `message`: `ThreadMessage`

  - `status?`: `ThreadAssistantMessage["status"]`
    - `type`: `"running"`

  - `metadata`: `ThreadMessage["metadata"]`

    - `unstable_state?`: `ReadonlyJSONValue | undefined` (experimental)

    - `unstable_annotations?`: `readonly ReadonlyJSONValue[] | undefined` (experimental)

    - `unstable_data?`: `readonly ReadonlyJSONValue[] | undefined` (experimental)

    - `steps?`: `readonly ThreadStep[] | undefined`

    - `submittedFeedback?`: `ThreadMessage["metadata"]["submittedFeedback"]`

      - `type`: `"positive" | "negative"`
      - `comment?`: `string`

    - `timing?`: `MessageTiming | undefined`

      - `streamStartTime`: `number`
      - `firstTokenTime?`: `number`
      - `totalStreamTime?`: `number`
      - `tokenCount?`: `number`
      - `tokensPerSecond?`: `number`
      - `totalChunks`: `number`
      - `toolCallCount`: `number`

    - `isOptimistic?`: `boolean` — Marks a client-side optimistic placeholder. Such messages are evicted once off the head branch and are never persisted.

    - `modality?`: `MessageModality | undefined` — Set when the message was produced by a voice session rather than typed.

    - `custom`: `Record<string, unknown>`

  - `attachments?`: `ThreadUserMessage["attachments"]`

  - `id`: `string`

  - `createdAt`: `Date`

  - `role`: `"system"`

  - `content`: `readonly [TextMessagePart]`

- `index`: `number`

- `isLast?`: `boolean`

- `branchNumber?`: `number`

- `branchCount?`: `number`

- `submission?`: `ComposerSubmission | undefined` — Set when this row is a composer submission rather than a thread message.

  - `id`: `string`

  - `role`: `MessageRole`

  - `text`: `string`

  - `quote?`: `QuoteInfo | undefined`

    - `text`: `string`
    - `messageId`: `string`

  - `attachments`: `readonly Attachment[]`

### PartByIndexProvider

- `index`: `number`

### ReadonlyThreadProvider

- `messages`: `readonly ThreadMessage[]`

### RuntimeAdapterProvider

- `adapters`: `RuntimeAdapters`

  - `modelContext?`: `ModelContextProvider | undefined`

    - `getModelContext`: `() => ModelContext`
    - `subscribe?`: `(callback: () => void) => Unsubscribe`

  - `history?`: `ThreadHistoryAdapter | undefined`

    - `scopeId?`: `string | undefined` — Stable identity for the storage scope read by LocalRuntime. Keep it the same when recreating the adapter for one thread, account, or workspace, and change it before loading a different scope. Adapters that omit it are treated as sharing one scope. External-history runtimes do not read it.
    - `unstable_copy?`: `(( branch: readonly ThreadMessage[], messageIds: readonly string[], ) => Promise<void>) | undefined` (experimental) — Keeps a copy of messages whose source of truth is the runtime's backend. \`branch\` is the conversation from its first message to its last, and \`messageIds\` names the ones in it that are new or changed; the adapter stores those, and first any earlier message of the branch it does not hold yet, keyed by each message's own id. It is undefined while the adapter keeps no copies, so the runtime then neither copies nor records tool interactions.
    - `load`: `() => Promise<ExportedMessageRepository & { state?: ReadonlyJSONValue; unstable_resume?: boolean; }>`
    - `resume?`: `(options: ChatModelRunOptions) => AsyncGenerator<ChatModelRunResult, void, unknown>`
    - `append`: `(item: ExportedMessageRepositoryItem) => Promise<void>`
    - `update?`: `(item: ExportedMessageRepositoryItem) => Promise<void>` — Rewrites a previously appended message in place, keyed by its message id. Adapters that implement this let a runtime persist a run paused for tool approval, finalize the same message once the run resumes, and record a tool result that arrives after the message settled, which can be a message later turns follow. Without it, a paused run is appended when it ends or when a later turn follows its message while the run is still open; anything that run adds after the append is not stored. An update may arrive for an id whose earlier write failed; treat it as an upsert keyed on the message id rather than assuming the entry exists.
    - `delete?`: `(items: ExportedMessageRepositoryItem[]) => Promise<void>` — Deletes messages from history. The runtime may send a second delete for the same message after a write it issued before the delete lands; treat deleting a missing entry as success.
    - `withFormat?`: `<TMessage, TStorageFormat extends Record<string, unknown>>(formatAdapter: MessageFormatAdapter<TMessage, TStorageFormat>) => GenericThreadHistoryAdapter<TMessage>` — Required when used with \`useAISDKRuntime\` / \`useChatRuntime\`.

  - `attachments?`: `AttachmentAdapter | undefined`

    - `accept`: `string`
    - `add`: `(state: { file: File; }) => Promise<PendingAttachment> | AsyncGenerator<PendingAttachment, void>` — Returns the pending attachment for the file, or an async generator yielding successive states of that one attachment. Every yield must have the same id.
    - `remove`: `(attachment: Attachment) => Promise<void>`
    - `send`: `(attachment: PendingAttachment, options?: { signal?: AbortSignal; }) => Promise<CompleteAttachment>`

  - `feedback?`: `FeedbackAdapter | undefined`
    - `submit`: `(feedback: FeedbackAdapterFeedback) => void`

- `children?`: `ReactNode`

### SuggestionByIndexProvider

- `index`: `number`

### TextMessagePartProvider

- `text`: `string`
- `isRunning?`: `boolean`

### ThreadListItemByIndexProvider

- `index`: `number`
- `archived`: `boolean`

### ThreadListItemRuntimeProvider

- `runtime`: `ThreadListItemRuntime`

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