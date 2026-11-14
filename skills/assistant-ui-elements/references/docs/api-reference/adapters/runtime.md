# Runtime Adapter Context
URL: /docs/api-reference/adapters/runtime

Provide assistant-ui runtime adapters through React context for model, attachment, speech, and feedback behavior.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### RuntimeAdapters

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