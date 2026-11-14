# Generative UI Spec
URL: /docs/api-reference/generative-ui/spec

The serializable node tree an assistant emits to describe generative UI. Covers the GenerativeUISpec format, its nodes, and the message part that carries the spec.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### GenerativeUIMessagePart

A message part that carries a JSON spec describing UI to render.

Render with `<MessagePrimitive.GenerativeUI components={...} />`. The primitive resolves component names against the consumer-provided allowlist — any unknown name throws a typed error rather than rendering. Stream- friendly: a partially-streamed spec renders progressively.

- `type`: `"generative-ui"`
- `spec`: `GenerativeUISpec` — The JSON spec describing the UI tree.
  - `root`: `GenerativeUINode | readonly GenerativeUINode[]` — Root node(s) to render.
- `id?`: `string` — A stable identity the host supplies for this part, used as its render and store key when non-empty and no other part of the same type in the message shares it; otherwise the part is keyed by its type and position.
- `parentId?`: `string`

### GenerativeUINode

A JSON spec describing a tree of UI components to render.

The agent emits a [GenerativeUIMessagePart](/docs/api-reference/generative-ui/spec#generativeuimessagepart) containing this spec, and the consumer-provided component allowlist is used to resolve `component` names. Any component referenced that is not present in the allowlist is rejected with a typed error — the allowlist is the security boundary in the default same-realm rendering path.

- `toString`: `(() => string) | ((radix?: number) => string) | (() => string) | (() => string)`
- `valueOf`: `(() => string) | (() => number) | (() => Object)`
- `toLocaleString`: `(() => string) | { (locales?: string | string[], options?: Intl.NumberFormatOptions): string; (locales?: Intl.LocalesArgument, options?: Intl.NumberFormatOptions): string; } | { (): string; (locales: string | string[], options?: Intl.NumberFormatOptions & Intl.DateTimeFormatOptions): string; }`

### GenerativeUISpec

The root spec for a generative UI tree.

- `root`: `GenerativeUINode | readonly GenerativeUINode[]` — Root node(s) to render.