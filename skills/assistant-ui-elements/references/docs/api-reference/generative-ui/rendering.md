# Generative UI Rendering
URL: /docs/api-reference/generative-ui/rendering

Render generative UI trees, build present-tool schemas, and inspect or serialize model-produced UI nodes.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### buildPresentParameters

Builds the JSON schema for the `present` tool from a [GenerativeUILibrary](/docs/api-reference/generative-ui/components#generativeuilibrary).

The model produces a node `{ _type, _key?,...props }` where `_type` selects a component, the optional `_key` pins a stable identity for list items that may reorder, and the rest are its props. The schema is a flat object: `_type` is an enum of the component names, every component's props are merged into one optional bag, and `children` recurses via `$defs` so the tree can nest. The reserved keys use the `_` spelling because Anthropic rejects `$` in tool schema property names; the renderer accepts either spelling.

It is intentionally flat rather than a per-`_type` discriminated union. Tool / function-call schemas (OpenAI and others) require the top-level parameters to be a plain object and reject a top-level `oneOf`/`anyOf`/`enum`. So props can't be refined per `_type` at the root; when components share a prop, its schema describes their distinct alternatives without tying them to `_type`. The model is guided by `_type`'s description and each prop schema. The renderer then drops a shared prop's value that the selected component's schema rejects and another declaring component's schema accepts.

- `library`: `GenerativeUILibrary`

### GenerativeUIRender

Internal renderer. Resolves a [GenerativeUISpec](/docs/api-reference/generative-ui/spec#generativeuispec) against the consumer allowlist. Used by `MessagePrimitive.GenerativeUI` and by `MessagePrimitive.Parts` when handling a `generative-ui` part.

- `spec`: `GenerativeUISpec` — The JSON spec to render.
  - `root`: `GenerativeUINode | readonly GenerativeUINode[]` — Root node(s) to render.
- `components`: `GenerativeUIComponentRegistry` — The component allowlist.
- `Fallback?`: `ComponentType<{ component: string; props?: unknown }> | undefined` — Optional fallback for unknown component names.

### GenerativeUIRenderContext

The render context threaded through [renderGenerativeUI](/docs/api-reference/generative-ui/rendering#rendergenerativeui).

- `status`: `GenerativeUIStatus` — Whether the tool call's arguments are still streaming or are complete.
- `dispatch?`: `GenerativeUIDispatch`
- `imageOrigins?`: `readonly string[]` — Origins allowed for vocabulary images. Omit to allow all sources.

### GenerativeUIRenderError

Thrown when a generative-ui spec references a component name that is not present in the consumer-provided allowlist. The allowlist is the security boundary in the same-realm rendering path — there is no fallback by default. Pass `Fallback` to opt into a soft-fail UX.

- `constructor?`: ``(componentName: string, message: string = `Component "${componentName}" is not in the generative-ui allowlist.`) => GenerativeUIRenderError``
- `componentName?`: `string`

### generativeUIToJSX

Serializes a generative-UI node to a JSX-like string for display: the "view source" of a model-produced tree. The wire form `{ _type: "Weather", id: "x" }` (or its `$type` spelling) becomes `<Weather id="x" />`, and nested `children` render between tags: `<Card title="Hi"><Text>hello</Text></Card>`. A model-provided `_key` or `$key` becomes the JSX `key` attribute, and an action in either spelling becomes the `$action` prop components receive.

By default this is a faithful textual rendering, not a parser: text children are emitted verbatim (not HTML/JSX-escaped), so the result is meant to be shown, not re-parsed. Returns `""` for nodes that aren't renderable (no type yet, `null`, booleans).

Pass `{ escape: true }` to make the output safe to paste back into JSX: any string child containing `<`, `>`, `&`, `{`, or `}` is emitted as a JSON-stringified expression (`{JSON.stringify(child)}`) instead of verbatim, while a string child with none of those characters still renders verbatim. An element whose type is not a valid JSX tag name, and a prop whose key is not a valid JSX attribute name, are left out, and an omitted element takes its children with it.

Pass `{ pretty: true }` to pretty-print nested element trees with two-space indentation, while pure text children stay on one line.

- `node`: `unknown`

- `options?`: `GenerativeUIToJSXOptions`

  - `escape?`: `boolean` — Escape string children so the output is safe to paste back into a JSX file. Defaults to \`false\`.
  - `pretty?`: `boolean` — Pretty-print nested element trees with two-space indentation. Defaults to \`false\`.

### renderGenerativeUI

Renders a generative-ui tree against a [GenerativeUILibrary](/docs/api-reference/generative-ui/components#generativeuilibrary).

The model emits each node as a flat object `{ _type,...props }`. We first normalize that wire form into the canonical NormalizedUINode (with `children` lifted to a reserved top-level key), then render: each `type` is looked up in the library and its `props` are passed to the component's `render(props)`, with `children` rendered recursively so components can nest. A prop that several components declare loses a value the component's own schema rejects when another declaring component's schema accepts it, since the merged `present` schema lets the model send either.

- `node`: `unknown`

- `library`: `GenerativeUILibrary`

- `context?`: `GenerativeUIRenderContext`

  - `status`: `GenerativeUIStatus` — Whether the tool call's arguments are still streaming or are complete.
  - `dispatch?`: `GenerativeUIDispatch`
  - `imageOrigins?`: `readonly string[]` — Origins allowed for vocabulary images. Omit to allow all sources.

### toSpecCatalog

Builds generative-frame spec mode from a generative UI library: a catalog with each component's props, slots, and events, and the React implementations `SpecRenderer` and `createSpecToolkit` render it with. Value controls emit their value as the event payload, and a control whose value prop is bound with `$bindState` follows that state and writes every change back to it. An entry that declares `events` or `slots` replaces a vocabulary component's built-in events, slots, and spec description (so an override should bring its own `description`), and its component receives rendered named slots in `$slots` and a setter for each bound prop in `$bindings`.

- `library?`: `GenerativeUILibrary`

- `options?`: `ToSpecCatalogOptions`

  - `actions?`: `Record<string, ActionDefinition>` — Host actions the model may bind to component events, next to the built-in \`setState\`.
  - `imageOrigins?`: `readonly string[]` — Origins allowed for vocabulary images. Omit to allow all sources.

### ToSpecCatalogOptions

Options for [toSpecCatalog](/docs/api-reference/generative-ui/rendering#tospeccatalog).

- `actions?`: `Record<string, ActionDefinition>` — Host actions the model may bind to component events, next to the built-in \`setState\`.
- `imageOrigins?`: `readonly string[]` — Origins allowed for vocabulary images. Omit to allow all sources.