# Persistence Adapters
URL: /docs/api-reference/adapters/persistence

Persistence adapters for saving assistant-ui message history, remote thread lists, and long-running chat sessions across browser reloads.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### ExportedMessageRepository

- `headId?`: `string | null`
- `messages`: `Array<{ message: ThreadMessage; parentId: string | null; runConfig?: RunConfig; }>`

### GenericThreadHistoryAdapter

- `load`: `() => Promise<MessageFormatRepository<TMessage>>`
- `pin?`: `() => void` — Snapshot the current thread so later writes survive a switch.
- `unstable_onReload?`: `(listener: () => Promise<void>) => () => void` (experimental) — Registers how the runtime re-reads the thread, for a history that follows a run another session drives. The history calls it to replace the loaded messages; returns the unsubscribe.
- `append`: `(item: MessageFormatItem<TMessage>) => Promise<void>`
- `update?`: `(item: MessageFormatItem<TMessage>, localMessageId: string) => Promise<void>`
- `delete?`: `(items: MessageFormatItem<TMessage>[]) => Promise<void>`
- `reportTelemetry?`: `(items: MessageFormatItem<TMessage>[], options?: { durationMs?: number; stepTimestamps?: { start_ms: number; end_ms: number; }[]; message?: ThreadMessage; userMessageId?: string; persistenceError?: unknown; }) => void`

### InMemoryThreadListAdapter

- `list?`: `() => Promise<RemoteThreadListResponse>`
- `rename?`: `(remoteId?: string, newTitle?: string) => Promise<void>`
- `updateCustom?`: `(remoteId?: string, custom?: Record<string, unknown>) => Promise<void>`
- `archive?`: `(remoteId?: string) => Promise<void>`
- `unarchive?`: `(remoteId?: string) => Promise<void>`
- `delete?`: `(remoteId?: string) => Promise<void>`
- `initialize?`: `(threadId: string, options?: { custom?: Record<string, unknown> | undefined }) => Promise<RemoteThreadInitializeResponse>`
- `register?`: `(threadId: string, externalId?: string | undefined, custom?: Record<string, unknown> | undefined) => RemoteThreadInitializeResponse`
- `generateTitle?`: `() => Promise<AssistantStream>`
- `fetch?`: `(threadId: string) => Promise<RemoteThreadMetadata>`

### MessageFormatAdapter

- `format`: `string`
- `encode`: `(item: MessageFormatItem<TMessage>) => TStorageFormat`
- `decode`: `(stored: MessageStorageEntry<TStorageFormat>) => MessageFormatItem<TMessage>`
- `getId`: `(message: TMessage) => string`

### RemoteThreadListAdapter

```
const runtime = useRemoteThreadListRuntime({
  adapter: myRemoteThreadListAdapter,
  runtimeHook: () => useLocalRuntime(chatModelAdapter),
});
```

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

### ThreadHistoryAdapter

```
const runtime = useLocalRuntime(chatModelAdapter, {
  adapters: {
    history: myHistoryAdapter,
  },
});
```

- `scopeId?`: `string | undefined` — Stable identity for the storage scope read by LocalRuntime. Keep it the same when recreating the adapter for one thread, account, or workspace, and change it before loading a different scope. Adapters that omit it are treated as sharing one scope. External-history runtimes do not read it.
- `unstable_copy?`: `(( branch: readonly ThreadMessage[], messageIds: readonly string[], ) => Promise<void>) | undefined` (experimental) — Keeps a copy of messages whose source of truth is the runtime's backend. \`branch\` is the conversation from its first message to its last, and \`messageIds\` names the ones in it that are new or changed; the adapter stores those, and first any earlier message of the branch it does not hold yet, keyed by each message's own id. It is undefined while the adapter keeps no copies, so the runtime then neither copies nor records tool interactions.
- `load`: `() => Promise<ExportedMessageRepository & { state?: ReadonlyJSONValue; unstable_resume?: boolean; }>`
- `resume?`: `(options: ChatModelRunOptions) => AsyncGenerator<ChatModelRunResult, void, unknown>`
- `append`: `(item: ExportedMessageRepositoryItem) => Promise<void>`
- `update?`: `(item: ExportedMessageRepositoryItem) => Promise<void>` — Rewrites a previously appended message in place, keyed by its message id. Adapters that implement this let a runtime persist a run paused for tool approval, finalize the same message once the run resumes, and record a tool result that arrives after the message settled, which can be a message later turns follow. Without it, a paused run is appended when it ends or when a later turn follows its message while the run is still open; anything that run adds after the append is not stored. An update may arrive for an id whose earlier write failed; treat it as an upsert keyed on the message id rather than assuming the entry exists.
- `delete?`: `(items: ExportedMessageRepositoryItem[]) => Promise<void>` — Deletes messages from history. The runtime may send a second delete for the same message after a write it issued before the delete lands; treat deleting a missing entry as success.
- `withFormat?`: `<TMessage, TStorageFormat extends Record<string, unknown>>(formatAdapter: MessageFormatAdapter<TMessage, TStorageFormat>) => GenericThreadHistoryAdapter<TMessage>` — Required when used with \`useAISDKRuntime\` / \`useChatRuntime\`.