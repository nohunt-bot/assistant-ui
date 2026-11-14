# MessageRuntime
URL: /docs/api-reference/runtimes/message-runtime

MessageRuntime state and actions for editing, reloading, copying, rating, speaking, and branching assistant-ui messages.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### MessageRuntime

- `path`: `MessageRuntimePath`

  - `ref`: `string`
  - `threadSelector`: `MessageRuntimePath["threadSelector"]`
    - `type`: `"main"`
  - `messageSelector`: `MessageRuntimePath["messageSelector"]`
    - `type`: `"messageId"`

- `composer`: `EditComposerRuntime`

  - `path`: `ComposerRuntimePath`

    - `ref`: `string`
    - `threadSelector`: `EditComposerRuntime["path"]["threadSelector"]`
      - `type`: `"main"`
    - `messageSelector`: `EditComposerRuntime["path"]["messageSelector"]`
      - `type`: `"messageId"`
    - `composerSource`: `"edit"`

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

  - `getState`: `() => EditComposerState`

  - `beginEdit`: `() => void`

  - `getAttachmentByIndex`: `(idx: number) => AttachmentRuntime & { source: "edit-composer"; }`

- `getState`: `() => MessageRuntimeState`

- `delete`: `() => void | Promise<void>`

- `reload`: `(config?: ReloadConfig) => void`

- `speak`: `() => void` (experimental)

- `stopSpeaking`: `() => void` (experimental)

- `submitFeedback`: `({ type, comment, }: { type: "positive" | "negative"; comment?: string; }) => void`

- `switchToBranch`: `({ position, branchId, }: { position?: "previous" | "next" | undefined; branchId?: string | undefined; }) => void`

- `unstable_getCopyText`: `() => string` (experimental)

- `subscribe`: `(callback: () => void) => Unsubscribe`

- `getMessagePartByIndex`: `(idx: number) => MessagePartRuntime`

- `getMessagePartByToolCallId`: `(toolCallId: string) => MessagePartRuntime`

- `getAttachmentByIndex`: `(idx: number) => AttachmentRuntime & { source: "message"; }`

### MessageRuntimeState

- `status?`: `ThreadAssistantMessage["status"]`
  - `type`: `"running"`

- `metadata`: `MessageRuntimeState["metadata"]`

  - `unstable_state?`: `ReadonlyJSONValue | undefined` (experimental)

  - `unstable_annotations?`: `readonly ReadonlyJSONValue[] | undefined` (experimental)

  - `unstable_data?`: `readonly ReadonlyJSONValue[] | undefined` (experimental)

  - `steps?`: `readonly ThreadStep[] | undefined`

  - `submittedFeedback?`: `MessageRuntimeState["metadata"]["submittedFeedback"]`

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

- `parentId`: `string | null`

- `index`: `number` — The position of this message in the thread (0 for first message)

- `isLast`: `boolean`

- `branchNumber`: `number`

- `branchCount`: `number`

- `speech?`: `SpeechState | undefined` (experimental)

  - `messageId`: `string`
  - `status`: `SpeechSynthesisAdapter.Status`
    - `type`: `"starting" | "running"`

### MessageState

> [!warn]
>
> **Deprecated.** Use `MessageRuntimeState`. From `@assistant-ui/react` 0.16, `MessageState` names the message state read through `useAuiState`.

- `status?`: `ThreadAssistantMessage["status"]`
  - `type`: `"running"`

- `metadata`: `MessageState["metadata"]`

  - `unstable_state?`: `ReadonlyJSONValue | undefined` (experimental)

  - `unstable_annotations?`: `readonly ReadonlyJSONValue[] | undefined` (experimental)

  - `unstable_data?`: `readonly ReadonlyJSONValue[] | undefined` (experimental)

  - `steps?`: `readonly ThreadStep[] | undefined`

  - `submittedFeedback?`: `MessageState["metadata"]["submittedFeedback"]`

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

- `parentId`: `string | null`

- `index`: `number` — The position of this message in the thread (0 for first message)

- `isLast`: `boolean`

- `branchNumber`: `number`

- `branchCount`: `number`

- `speech?`: `SpeechState | undefined` (experimental)

  - `messageId`: `string`
  - `status`: `SpeechSynthesisAdapter.Status`
    - `type`: `"starting" | "running"`