# Composer Trigger Hooks
URL: /docs/api-reference/hooks/composer-triggers

Unstable assistant-ui hooks for mention menus, slash commands, and custom composer trigger popovers.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### unstable_useLiveCompletionAdapter

> [!tip]
>
> **Experimental.** Shipped 2026-06-15; it may change in any release.

Bridges an async completion source (a server search, a gateway RPC) into the synchronous `Unstable_TriggerAdapter` that `ComposerTriggerPopover` consumes. `search(query)` returns the last fetched items synchronously and schedules a debounced fetch when the query changes; when results arrive the returned `adapter` identity changes, which re-runs the popover's lookup so the fresh items render. This is a search-only adapter (`categories` are empty).

`isLoading` is `true` while a fetch is in flight. Pass it to the popover's `isLoading` prop to render a loading state.

```
const mentions = unstable_useLiveCompletionAdapter({
  fetcher: (query) => searchUsers(query),
});

<ComposerTriggerPopover
  char="@"
  adapter={mentions.adapter}
  isLoading={mentions.isLoading}
  directive={{ onInserted }}
/>
```

- `options`: `Unstable_UseLiveCompletionAdapterOptions`

  - `fetcher`: `(query: string) => Promise<readonly Unstable_TriggerItem[]>` — Fetches the items for a query from an async source. Called debounced; the resolved items are cached and returned synchronously to the popover on the next render.
  - `cacheKey?`: `string | number | undefined` — Identifies the fetcher's data source. Change this when switching accounts, workspaces, or another boundary that should invalidate cached results.
  - `debounceMs`: `number | undefined` (default `60`) — Debounce applied before a fetch fires, in milliseconds.
  - `enabled`: `boolean | undefined` (default `true`) — When \`false\`, no fetch is scheduled and the adapter stays empty.

### unstable_useMentionAdapter

> [!tip]
>
> **Experimental.** Shipped 2026-04-15; it may change in any release.

Creates a spreadable `{ adapter, directive }` bundle for `@` mentions. Supports tools registered in model context, explicit items, or both — flat or categorized.

```
const mention = unstable_useMentionAdapter();
<ComposerTriggerPopover char="@" {...mention} />
```

- `options?`: `Unstable_UseMentionAdapterOptions`

  - `items?`: `readonly Unstable_Mention[]` — Flat mention list. Ignored when \`categories\` is set, and keeps the adapter flat when a tool \`category\` is configured.

  - `categories?`: `readonly Unstable_MentionCategory[]` — Categorized mentions for drill-down navigation.

  - `includeModelContextTools?`: `boolean | Unstable_ModelContextToolsOptions` — How tools registered in model context integrate. - \`false\`: exclude. - \`true\`: include (default when no \`items\`/\`categories\`; as a category if \`categories\` is set, flat otherwise). - object: explicit config; \`category\` also selects drill-down mode. Omitted → defaults to \`true\` iff neither \`items\` nor \`categories\`.

  - `formatter`: `Unstable_DirectiveFormatter` (default `unstable_defaultDirectiveFormatter`) — Directive formatter.

    - `serialize`: `(item: TriggerItem) => string` — Serialize a trigger item to directive text.
    - `parse`: `(text: string) => readonly DirectiveSegment[]` — Parse text into alternating text and directive segments.

  - `onInserted?`: `(item: Unstable_TriggerItem) => void` — Fires after an item is inserted into the composer.

  - `iconMap?`: `Record<string, Unstable_IconComponent>` — Maps \`metadata.icon\` / \`category.id\` string keys to React components.

  - `fallbackIcon?`: `Unstable_IconComponent` — Fallback icon when no entry in \`iconMap\` matches.

### unstable_useSlashCommandAdapter

> [!tip]
>
> **Experimental.** Shipped 2026-04-06; it may change in any release.

Bundles slash command definitions (with inline `execute` callbacks) into `{adapter, action}` that plug directly into `ComposerTriggerPopover`. `execute` stays in the hook closure and is never attached to the returned `TriggerItem`, keeping items serializable.

```
const slash = unstable_useSlashCommandAdapter({
  commands: [
    { id: "summarize", execute: () => runSummarize(), icon: "FileText" },
    { id: "translate", execute: () => runTranslate(), icon: "Languages" },
  ],
});

<ComposerTriggerPopover char="/" {...slash} />
```

- `options`: `Unstable_UseSlashCommandAdapterOptions`

  - `commands`: `readonly Unstable_SlashCommand[]`
  - `removeOnExecute`: `boolean | undefined` (default `false`) — Strip the trigger text from the composer after executing.
  - `iconMap?`: `Record<string, Unstable_IconComponent>` — Maps \`metadata.icon\` / \`category.id\` string keys to React components.
  - `fallbackIcon?`: `Unstable_IconComponent` — Fallback icon when no entry in \`iconMap\` matches.

### unstable_useTriggerPopoverRootContext

> [!tip]
>
> **Experimental.** Shipped 2026-04-15; it may change in any release.

```
const unstable_useTriggerPopoverRootContext: () => TriggerPopoverRootContextValue;
```

### unstable_useTriggerPopoverRootContextOptional

> [!tip]
>
> **Experimental.** Shipped 2026-04-15; it may change in any release.

```
const unstable_useTriggerPopoverRootContextOptional: () => TriggerPopoverRootContextValue | null;
```

### unstable_useTriggerPopoverScopeContext

> [!tip]
>
> **Experimental.** Shipped 2026-04-15; it may change in any release.

```
const unstable_useTriggerPopoverScopeContext: () => TriggerPopoverResourceOutput;
```

### unstable_useTriggerPopoverScopeContextOptional

> [!tip]
>
> **Experimental.** Shipped 2026-04-15; it may change in any release.

```
const unstable_useTriggerPopoverScopeContextOptional: () => TriggerPopoverResourceOutput | null;
```

### unstable_useTriggerPopoverTriggers

> [!tip]
>
> **Experimental.** Shipped 2026-04-15; it may change in any release.

Live map of registered triggers, re-rendering on change. Prefer `subscribeLifecycle` for incremental add/remove handling.

```
const unstable_useTriggerPopoverTriggers: () => ReadonlyMap<string, RegisteredTrigger>;
```

### unstable_useTriggerPopoverTriggersOptional

> [!tip]
>
> **Experimental.** Shipped 2026-04-15; it may change in any release.

Like `useTriggerPopoverTriggers` but returns an empty map outside a root.

```
const unstable_useTriggerPopoverTriggersOptional: () => ReadonlyMap<string, RegisteredTrigger>;
```