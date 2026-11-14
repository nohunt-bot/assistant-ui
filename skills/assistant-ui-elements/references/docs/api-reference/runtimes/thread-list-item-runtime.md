# ThreadListItemRuntime
URL: /docs/api-reference/runtimes/thread-list-item-runtime

ThreadListItemRuntime state and actions for selecting, archiving, unarchiving, deleting, and renaming assistant-ui conversations.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### ThreadListItemRuntime

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

### ThreadListItemRuntimeState

- `isMain`: `boolean`
- `isRunning`: `boolean` — Whether this thread has a run in progress, including a run that continues after the user switches to another thread.
- `id`: `string`
- `remoteId?`: `string | undefined`
- `externalId?`: `string | undefined`
- `status`: `ThreadListItemStatus`
- `title?`: `string | undefined`
- `lastMessageAt?`: `Date | undefined`
- `custom?`: `Record<string, unknown> | undefined`

### ThreadListItemState

> [!warn]
>
> **Deprecated.** Use `ThreadListItemRuntimeState`. From `@assistant-ui/react` 0.16, `ThreadListItemState` names the thread list item state read through `useAuiState`.

- `isMain`: `boolean`
- `isRunning`: `boolean` — Whether this thread has a run in progress, including a run that continues after the user switches to another thread.
- `id`: `string`
- `remoteId?`: `string | undefined`
- `externalId?`: `string | undefined`
- `status`: `ThreadListItemStatus`
- `title?`: `string | undefined`
- `lastMessageAt?`: `Date | undefined`
- `custom?`: `Record<string, unknown> | undefined`