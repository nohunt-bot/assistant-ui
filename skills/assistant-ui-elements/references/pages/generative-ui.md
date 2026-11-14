# Generative UI
URL: /elements/generative-ui

A styled component library for rendering structured generative UI output.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

`styledGenerativeUILibrary` is `@assistant-ui/generative-ui`'s default component vocabulary (headings, text, cards, lists, and more) with three entries swapped: its `Markdown` component renders with `react-markdown` and GitHub-flavored markdown instead of dumping the raw source as plain text, `Diagram` draws its Mermaid source with the [Mermaid diagram](/elements/mermaid-diagram) element, and `Map` puts its places on the [Geo map](/elements/geo-map) element. With a runtime the library backs a tool the model calls to render structured UI; standalone you call `renderGenerativeUI` yourself with any spec built against the same library.

## Getting started

**With a runtime:**

The library only supplies components; something still has to expose them to the model and mount the result when it calls them. `JSONGenerativeUI` builds that bridge: a tool whose parameters come from the library's Zod schemas, and whose render draws the model's response through the library.

1. ### Build a toolkit around the library

   ```
   import { defineToolkit } from "@assistant-ui/react";
   import { JSONGenerativeUI } from "@assistant-ui/generative-ui/react";
   import { styledGenerativeUILibrary } from "@/components/assistant-ui/elements/generative-ui";

   const generative = new JSONGenerativeUI({ library: styledGenerativeUILibrary });

   export default defineToolkit({
     present: generative.present(),
   });
   ```

2. ### Register it on your runtime

   ```
   "use client";

   import { AssistantRuntimeProvider, AuiConfig, Tools } from "@assistant-ui/react";
   import { useChatRuntime } from "@assistant-ui/ai-sdk";
   import { lastAssistantMessageIsCompleteWithToolCalls } from "ai";
   import { Thread } from "@/components/assistant-ui/elements/thread.aui";
   import toolkit from "./toolkit";

   const config = AuiConfig({ tools: Tools({ toolkit }) });

   export default function Page() {
     const runtime = useChatRuntime({
       sendAutomaticallyWhen: lastAssistantMessageIsCompleteWithToolCalls,
     });
     return (
       <AssistantRuntimeProvider config={config} runtime={runtime}>
         <Thread />
       </AssistantRuntimeProvider>
     );
   }
   ```

   The model can now call `present` with a `{ _type, ...props }` tree; it renders through `styledGenerativeUILibrary`, so a `Markdown` node comes out fully formatted instead of as raw text.

**Standalone (no runtime):**

Standalone, skip the tool: call `renderGenerativeUI` with any spec built against the library.

1. ### Render a spec directly

   ```
   import { renderGenerativeUI } from "@assistant-ui/generative-ui/react";
   import { styledGenerativeUILibrary } from "@/components/assistant-ui/elements/generative-ui";

   function Report() {
     return renderGenerativeUI(
       { $type: "Markdown", value: "**Revenue** is up 12% this quarter." },
       styledGenerativeUILibrary,
     );
   }
   ```

   Each node selects a component by `$type` and passes its other keys as props; nest further nodes under a `children` key.

## Examples

### See the override in action

`defaultGenerativeUILibrary.Markdown` renders `value` as plain text; `styledGenerativeUILibrary.Markdown` parses the same string as GitHub-flavored markdown. Both wrap the result in the same `<div data-aui="markdown">`, so any styling already targeting that attribute keeps applying.

```
import { defaultGenerativeUILibrary, renderGenerativeUI } from "@assistant-ui/generative-ui/react";
import { styledGenerativeUILibrary } from "@/components/assistant-ui/elements/generative-ui";

renderGenerativeUI({ $type: "Markdown", value: "**bold**" }, defaultGenerativeUILibrary); // <div data-aui="markdown">**bold**</div>
renderGenerativeUI({ $type: "Markdown", value: "**bold**" }, styledGenerativeUILibrary); // <div data-aui="markdown"><strong>bold</strong></div>
```

### Extend the library with your own component

Add to it with a plain object spread. No special wrapper is required for a library that is not split across a client and a server build.

```
import { JSONGenerativeUI } from "@assistant-ui/generative-ui/react";
import { styledGenerativeUILibrary } from "@/components/assistant-ui/elements/generative-ui";
import { z } from "zod";

const library = {
  ...styledGenerativeUILibrary,
  Callout: {
    description: "A highlighted note.",
    properties: z.object({ text: z.string() }),
    render: ({ text }: { text: string }) => <div data-aui="callout">{text}</div>,
  },
};

const generative = new JSONGenerativeUI({ library });
```

### Use the Markdown renderer on its own

Install `elements-generative-markdown` to use the same Markdown renderer independently of the diagram and map renderers. It is also installed by `generative-ui`.

```
npx assistant-ui@latest add elements-generative-markdown
```

```
import { GenerativeMarkdown } from "@/components/assistant-ui/elements/generative-markdown";

<GenerativeMarkdown value="**Revenue** is up 12% this quarter." />;
```

`value` may be absent while content streams. Nested `children` render after the Markdown. In a `"use generative"` toolkit, set `render: GenerativeMarkdown` inside `defineGenerativeComponents` to reuse this renderer.

## API reference

**With a runtime:**

### JSONGenerativeUI

| Member                          | Parameters                                                   | Description                                                                                                                                                                                                                                |
| ------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `new JSONGenerativeUI(options)` | `{ library: GenerativeUILibrary; actions?: ActionRegistry }` | Builds the tool's parameter schema once, from the library's Zod `properties`.                                                                                                                                                              |
| `.present(options?)`            | `{ display?: "standalone" }`, returns a `PresentTool`        | A frontend tool: parameters are the library's schema, and calling it renders the model's `{ _type, ...props }` tree through the library. `display: "standalone"` surfaces the result outside the chain-of-thought trace instead of inline. |
| `.promptUser()`                 | none, returns a `PromptUserTool`                             | The same rendering, as a human-in-the-loop tool: the model pauses until the rendered UI supplies a result.                                                                                                                                 |

Register the resulting tool the way you register any other: `defineToolkit({ present: generative.present() })`, then `Tools({ toolkit })` into your `AuiConfig`.

**Standalone (no runtime):**

### renderGenerativeUI

| Parameter | Type                                                   | Description                                                                                                                                                                       |
| --------- | ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `node`    | `unknown`                                              | A `{ $type, ...props }` or `{ _type, ...props }` tree, or an array of them. The type selects the component; every other key becomes a prop; a `children` key nests further nodes. |
| `library` | `GenerativeUILibrary`                                  | Looked up by the node's type. An unknown type logs a console error in development and renders nothing.                                                                            |
| `context` | `{ status: "streaming" \| "done"; dispatch?(action) }` | Optional, defaults to `{ status: "done" }`. A component whose `streamProperties` is not `true` renders nothing while `status` is `"streaming"`.                                   |

`styledGenerativeUILibrary` itself takes no props: it is a `GenerativeUILibrary` value, the same shape as `defaultGenerativeUILibrary`, with its `Markdown` entry replaced.