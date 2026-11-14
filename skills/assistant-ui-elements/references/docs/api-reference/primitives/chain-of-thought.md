# ChainOfThoughtPrimitive
URL: /docs/api-reference/primitives/chain-of-thought

Chain of thought primitives for rendering assistant reasoning, step lists, and collapsible disclosure UI in message content.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

For examples and usage patterns, see [ChainOfThought](/docs/primitives/chain-of-thought).

## API Reference

### Root

The root container for chain of thought components. This component provides a wrapper for chain of thought content, including reasoning and tool-call parts that can be collapsed in an accordion.

This primitive renders a `<div>` element unless `asChild` is set.

```
<ChainOfThoughtPrimitive.Root>
  <ChainOfThoughtPrimitive.AccordionTrigger>
    Toggle reasoning
  </ChainOfThoughtPrimitive.AccordionTrigger>
  <ChainOfThoughtPrimitive.Parts />
</ChainOfThoughtPrimitive.Root>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### AccordionTrigger

A button component that toggles the collapsed state of the chain of thought accordion. This component automatically handles the toggle functionality, expanding or collapsing the chain of thought parts when clicked. Exposes the current disclosure state through \`aria-expanded\`.

This primitive renders a `<button>` element unless `asChild` is set.

```
<ChainOfThoughtPrimitive.AccordionTrigger>
  Toggle Reasoning
</ChainOfThoughtPrimitive.AccordionTrigger>
```

- `asChild`: `boolean | undefined` (default `false`) — Change the default rendered element for the one passed as a child, merging their props and behavior.\
  \
  Read the [Composition](/docs/api-reference/primitives/composition) guide for more details.
- `render?`: `ReactElement | undefined`

### Parts

Renders the parts within a chain of thought, with support for collapsed/expanded states. When collapsed, no parts are shown. When expanded, all parts are rendered using the provided component configuration through the part scope mechanism.

- `components?`: `ChainOfThoughtPartsComponentConfig` (deprecated: Use the children render function instead.)

  - `Reasoning?`: `ReasoningMessagePartComponent | undefined` — Component for rendering reasoning parts
  - `tools?`: `ChainOfThoughtPartsComponentConfig["tools"]` — Fallback component for tool-call parts
    - `Fallback?`: `ToolCallMessagePartComponent | undefined`
  - `Layout?`: `ComponentType<PropsWithChildren> | undefined` — Layout component to wrap the rendered parts when expanded

- `children?`: `(value: { part: PartState }) => ReactNode` — Render function called for each part. Receives the part.