# Confidence
URL: /elements/confidence-marker

Which claims came from a source, which were inferred, and which are guesses.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

ConfidenceMarker underlines each claim in an answer by how sure it is: grounded, inferred, or uncertain, with the basis shown on hover or focus. With a runtime you produce the claim list from a structured tool result; standalone you already hold it.

## Getting started

**With a runtime:**

assistant-ui's message content has no built-in per-claim confidence concept: text and citations are separate content parts, and neither carries a confidence grade. The closest real building block is a tool call. Ask the model to return its answer as graded claims, and read the result back off the `tool-call` part.

1. ### Read the tool result off the message

   ```
   "use client";

   import { useState } from "react";
   import { useAuiState } from "@assistant-ui/react";
   import type { ToolCallMessagePart } from "@assistant-ui/react";
   import {
     ConfidenceMarker,
     type ConfidenceClaim,
   } from "@/components/assistant-ui/elements/confidence-marker";

   type CiteClaims = ToolCallMessagePart<unknown, { claims: ConfidenceClaim[] }>;

   function ClaimsFromMessage() {
     const [hoveredId, setHoveredId] = useState("");
     const claims = useAuiState((s) =>
       s.message.role === "assistant"
         ? s.message.content.find(
             (part): part is CiteClaims =>
               part.type === "tool-call" && part.toolName === "cite_claims",
           )?.result?.claims
         : undefined,
     );

     if (!claims) return null;

     return <ConfidenceMarker claims={claims} hoveredId={hoveredId} onHover={setHoveredId} />;
   }
   ```

   `cite_claims` and the `{ claims: [...] }` result shape are your own tool contract; `part.result` carries whatever you defined back to the client, typed by the generic you give `ToolCallMessagePart`.

2. ### Use a real citation for the grounded tier alone

   When all you need is "this claim came from a fetched source," not a full three-tier grade, `SourceMessagePart` is a first-class runtime concept you can render directly instead of routing it through a custom tool. See [A grounded-only version](#a-grounded-only-version) below.

**Standalone (no runtime):**

Standalone, you already hold the claim list; the component only tracks which one is hovered or focused.

1. ### Hold the claims and the hover state

   ```
   "use client";

   import { useState } from "react";
   import {
     ConfidenceMarker,
     type ConfidenceClaim,
   } from "@/components/assistant-ui/elements/confidence-marker";

   const CLAIMS: ConfidenceClaim[] = [
     { id: "1", text: "The composer owns its draft from 0.14 onward.", confidence: "grounded", basis: "migration-0.14.md" },
     { id: "2", text: "Most apps will not need the hydrate effect.", confidence: "inferred", basis: "from the changed API" },
   ];

   export function Answer() {
     const [hoveredId, setHoveredId] = useState("");
     return <ConfidenceMarker claims={CLAIMS} hoveredId={hoveredId} onHover={setHoveredId} />;
   }
   ```

2. ### Basis is plain text

   `basis` is a plain string; nothing parses it. Citing a document path, a search result title, or "not measured" are all equally valid values.

## Anatomy

```
<div data-slot="confidence-marker">
  <p>{/* each claim as an inline button, underlined by confidence tier */}</p>
  <div>{/* fixed-height slot: the hovered/focused claim's basis pill, or nothing */}</div>
</div>
```

Hover and focus both reveal the basis (`onMouseEnter`/`onFocus` share the same `onHover` call), so it is reachable from the keyboard, and `aria-describedby` points at the basis pill only while it belongs to the focused claim. The basis slot has a fixed height whether or not anything is shown, so revealing it never shifts the paragraph above. The tier is marked by underline style, not color alone: solid for grounded and inferred, dotted for uncertain, so the distinction survives without color.

## Examples

### A grounded-only version

**With a runtime:**

Mapping real citations straight into the same shape skips the custom tool entirely when every claim you show is source-backed:

```
const claims: ConfidenceClaim[] = message.content
  .filter((part): part is SourceMessagePart => part.type === "source")
  .map((part) => ({
    id: part.id,
    text: part.title ?? part.url ?? part.id,
    confidence: "grounded",
    basis: part.sourceType === "url" ? part.url : (part.filename ?? part.title),
  }));
```

**Standalone (no runtime):**

The same narrowing works on any list you already hold: keep only the claims whose confidence is `"grounded"` when a caller only wants the sourced ones.

```
<ConfidenceMarker claims={claims.filter((c) => c.confidence === "grounded")} hoveredId={hoveredId} onHover={setHoveredId} />
```

### Restyle the underline and basis pill

Both lanes take `className` on the root. The basis pill reads the shared `floating` and `mono` surfaces from `surfaces.tsx`.

```
<ConfidenceMarker className="gap-3" /* ... */ />
```

## API reference

**With a runtime:**

### Message state

| Selector / field             | Type                                                                                                               | Description                                                                                                                  |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------- |
| `s.message.content`          | `readonly ThreadAssistantMessagePart[]`                                                                            | Search this for a `tool-call` part whose `result` you shape as a claims list; assistant-ui has no built-in confidence field. |
| `ToolCallMessagePart.result` | `TResult \| undefined`                                                                                             | The tool's return value once it has run, typed by the generic you give `ToolCallMessagePart<TArgs, TResult>`.                |
| `SourceMessagePart`          | `{ type: "source"; sourceType: "url" \| "document"; id: string; url?: string; title?: string; filename?: string }` | A real citation part, useful when "grounded" is the only tier you need.                                                      |

**Standalone (no runtime):**

### ConfidenceMarker

| Prop        | Type                         | Default  | Description                                                                 |
| ----------- | ---------------------------- | -------- | --------------------------------------------------------------------------- |
| `claims`    | `readonly ConfidenceClaim[]` | required | The full, ordered list to render.                                           |
| `hoveredId` | `string`                     | required | Id of the claim whose basis is shown; an empty string shows none.           |
| `onHover`   | `(id: string) => void`       |          | Called with the claim id on hover or focus, and with `""` on leave or blur. |
| `className` | `string`                     |          | Merged onto the root.                                                       |

### ConfidenceClaim

| Field        | Type                                      | Description                           |
| ------------ | ----------------------------------------- | ------------------------------------- |
| `id`         | `string`                                  |                                       |
| `text`       | `string`                                  |                                       |
| `confidence` | `"grounded" \| "inferred" \| "uncertain"` | Drives the underline color and style. |
| `basis`      | `string`                                  | Shown in the hover/focus pill.        |

All other `div` props are forwarded to the root.