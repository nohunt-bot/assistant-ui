# Attachment Adapters
URL: /docs/api-reference/adapters/attachments

Attachment adapters for uploading files, handling lifecycle events, and bringing app-owned content into assistant-ui composers and messages.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### AttachmentAdapter

- `accept`: `string`
- `add`: `(state: { file: File; }) => Promise<PendingAttachment> | AsyncGenerator<PendingAttachment, void>` — Returns the pending attachment for the file, or an async generator yielding successive states of that one attachment. Every yield must have the same id.
- `remove`: `(attachment: Attachment) => Promise<void>`
- `send`: `(attachment: PendingAttachment, options?: { signal?: AbortSignal; }) => Promise<CompleteAttachment>`

### CloudFileAttachmentAdapter

- `constructor?`: `(cloud: AssistantCloud | (() => AssistantCloud)) => CloudFileAttachmentAdapter`
- `accept?`: `string`
- `add?`: `({ file, }: { file: File; }) => AsyncGenerator<PendingAttachment, void>`
- `remove?`: `(attachment: Attachment) => Promise<void>`
- `send?`: `(attachment: PendingAttachment) => Promise<CompleteAttachment>`

### CompositeAttachmentAdapter

- `constructor?`: `(adapters: AttachmentAdapter[]) => CompositeAttachmentAdapter`
- `accept?`: `string`
- `add?`: `(state: { file: File }) => Promise<PendingAttachment> | AsyncGenerator<PendingAttachment, void, any>`
- `send?`: `(attachment: PendingAttachment, options?: { signal?: AbortSignal }) => Promise<CompleteAttachment>`
- `remove?`: `(attachment: Attachment) => Promise<void>`

### SimpleImageAttachmentAdapter

- `accept?`: `string`
- `add?`: `(state: { file: File }) => Promise<PendingAttachment>`
- `send?`: `(attachment: PendingAttachment, options?: { signal?: AbortSignal }) => Promise<CompleteAttachment>`
- `remove?`: `() => Promise<void>`

### SimpleTextAttachmentAdapter

- `accept?`: `string`
- `add?`: `(state: { file: File }) => Promise<PendingAttachment>`
- `send?`: `(attachment: PendingAttachment, options?: { signal?: AbortSignal }) => Promise<CompleteAttachment>`
- `remove?`: `() => Promise<void>`