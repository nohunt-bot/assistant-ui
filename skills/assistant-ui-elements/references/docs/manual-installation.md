# Manual Installation
URL: /docs/manual-installation

Install assistant-ui by hand with the CLI or the shadcn registry, then add your first chat component.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

> [!info]
>
> **Prefer the guided setup?**
>
> Tell your coding agent to `install and set up assistant-ui`; see [Installation](/docs/installation). Agents already in a setup can follow these steps.

## Quick Start

1. ### Initialize assistant-ui

   **Add to a project:**

   ```
   npx assistant-ui@latest init
   ```

2. ### Add API key

   Create a `.env` file with your API key:

   ```
   OPENAI_API_KEY="sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   ```

   Developing locally with a ChatGPT Plus or Pro plan? You can skip the API key and run on your subscription instead; see [ChatGPT Subscription](/docs/guides/chatgpt-subscription).

3. ### Start the app

   ```
   npm run dev
   ```

## Manual Setup

If you prefer not to use the CLI, you can install components manually.

Add the assistant-ui registry to your `components.json` so the shadcn CLI resolves components for your style. Styles whose name starts with `base-` receive Base UI flavored components; all other styles receive the Radix flavored ones. `npx shadcn init` defaults to Base UI styles (for example `base-nova`), so new projects need this style-aware URL for components that compile against that stack.

```
{
  "registries": {
    "@assistant-ui": "https://r.assistant-ui.com/styles/{style}/{name}.json"
  }
}
```

Existing Radix projects can keep the plain form `https://r.assistant-ui.com/{name}.json` as a fallback (for example `npx shadcn@latest add https://r.assistant-ui.com/thread.json`).

1. ### Add assistant-ui

   With the style-aware registry configured in components.json ("@assistant-ui": "https\://r.assistant-ui.com/styles/{style}/{name}.json"), the flavor resolves from the project style automatically:

   ```bash
   npx shadcn@latest add @assistant-ui/thread @assistant-ui/thread-list
   ```

   Or add by direct URL without registry configuration:

   ```bash
   npx shadcn@latest add https://r.assistant-ui.com/base/thread.json https://r.assistant-ui.com/base/thread-list.json
   ```

   Or install manually:

   ```bash
   npm install @assistant-ui/react@^0.15.36 @assistant-ui/react-markdown@^0.14.26 @base-ui/react class-variance-authority remark-gfm tw-shimmer@^0.4.14 zustand
   ```

   ```bash
   npx shadcn@latest add button skeleton dialog tooltip avatar textarea input
   ```

   Then copy these files (packaged registry content; the source links show where each ships from):

   - [components/assistant-ui/elements/thread.aui.tsx](https://r.assistant-ui.com/base/files/thread/components/assistant-ui/elements/thread.aui.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/thread.aui.tsx))
   - [components/assistant-ui/elements/thread-list.aui.tsx](https://r.assistant-ui.com/base/files/thread-list/components/assistant-ui/elements/thread-list.aui.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/thread-list.aui.tsx))
   - [components/assistant-ui/elements/attachment.aui.tsx](https://r.assistant-ui.com/base/files/attachment/components/assistant-ui/elements/attachment.aui.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/attachment.aui.tsx))
   - [components/assistant-ui/elements/tooltip-icon-button.tsx](https://r.assistant-ui.com/base/files/tooltip-icon-button/components/assistant-ui/elements/tooltip-icon-button.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/tooltip-icon-button.tsx))
   - [components/assistant-ui/elements/attachment-state.ts](https://r.assistant-ui.com/base/files/elements-attachment-state/components/assistant-ui/elements/attachment-state.ts) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/attachment-state.ts))
   - [hooks/use-attachment-src.ts](https://r.assistant-ui.com/base/files/use-attachment-src/hooks/use-attachment-src.ts) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/hooks/use-attachment-src.ts))
   - [components/assistant-ui/elements/file.tsx](https://r.assistant-ui.com/base/files/file/components/assistant-ui/elements/file.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/file.tsx))
   - [components/assistant-ui/elements/media-player.tsx](https://r.assistant-ui.com/base/files/elements-media-player/components/assistant-ui/elements/media-player.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/media-player.tsx))
   - [components/assistant-ui/elements/surfaces.tsx](https://r.assistant-ui.com/base/files/elements-surfaces/components/assistant-ui/elements/surfaces.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/surfaces.tsx))
   - [components/assistant-ui/utils/useIsomorphicLayoutEffect.ts](https://r.assistant-ui.com/base/files/elements-surfaces/components/assistant-ui/utils/useIsomorphicLayoutEffect.ts) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/utils/useIsomorphicLayoutEffect.ts))
   - [components/assistant-ui/elements/follow-up-suggestions.aui.tsx](https://r.assistant-ui.com/base/files/follow-up-suggestions/components/assistant-ui/elements/follow-up-suggestions.aui.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/follow-up-suggestions.aui.tsx))
   - [components/assistant-ui/elements/image.tsx](https://r.assistant-ui.com/base/files/image/components/assistant-ui/elements/image.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/image.tsx))
   - [components/assistant-ui/utils/href.ts](https://r.assistant-ui.com/base/files/elements-href/components/assistant-ui/utils/href.ts) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/utils/href.ts))
   - [components/assistant-ui/elements/markdown-text.tsx](https://r.assistant-ui.com/base/files/markdown-text/components/assistant-ui/elements/markdown-text.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/markdown-text.tsx))
   - [hooks/use-copy-to-clipboard.ts](https://r.assistant-ui.com/base/files/use-copy-to-clipboard/hooks/use-copy-to-clipboard.ts) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/hooks/use-copy-to-clipboard.ts))
   - [components/assistant-ui/elements/reasoning.aui.tsx](https://r.assistant-ui.com/base/files/reasoning/components/assistant-ui/elements/reasoning.aui.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/reasoning.aui.tsx))
   - [components/assistant-ui/elements/reasoning.tsx](https://r.assistant-ui.com/base/files/elements-reasoning/components/assistant-ui/elements/reasoning.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/reasoning.tsx))
   - [components/assistant-ui/utils/useIsomorphicLayoutEffect.ts](https://r.assistant-ui.com/base/files/elements-reasoning/components/assistant-ui/utils/useIsomorphicLayoutEffect.ts) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/utils/useIsomorphicLayoutEffect.ts))
   - [components/assistant-ui/elements/collapsible-root.tsx](https://r.assistant-ui.com/base/files/elements-collapsible-root/components/assistant-ui/elements/collapsible-root.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/collapsible-root.tsx))
   - [components/assistant-ui/elements/tool-fallback.aui.tsx](https://r.assistant-ui.com/base/files/tool-fallback/components/assistant-ui/elements/tool-fallback.aui.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/tool-fallback.aui.tsx))
   - [components/assistant-ui/elements/tool-group.aui.tsx](https://r.assistant-ui.com/base/files/tool-group/components/assistant-ui/elements/tool-group.aui.tsx) ([source](https://github.com/assistant-ui/assistant-ui/blob/main/packages/ui/src/components/react/assistant-ui/elements/tool-group.aui.tsx))

   ```bash
   curl -fsSL --create-dirs \
     -o 'components/assistant-ui/elements/thread.aui.tsx' https://r.assistant-ui.com/base/files/thread/components/assistant-ui/elements/thread.aui.tsx \
     -o 'components/assistant-ui/elements/thread-list.aui.tsx' https://r.assistant-ui.com/base/files/thread-list/components/assistant-ui/elements/thread-list.aui.tsx \
     -o 'components/assistant-ui/elements/attachment.aui.tsx' https://r.assistant-ui.com/base/files/attachment/components/assistant-ui/elements/attachment.aui.tsx \
     -o 'components/assistant-ui/elements/tooltip-icon-button.tsx' https://r.assistant-ui.com/base/files/tooltip-icon-button/components/assistant-ui/elements/tooltip-icon-button.tsx \
     -o 'components/assistant-ui/elements/attachment-state.ts' https://r.assistant-ui.com/base/files/elements-attachment-state/components/assistant-ui/elements/attachment-state.ts \
     -o 'hooks/use-attachment-src.ts' https://r.assistant-ui.com/base/files/use-attachment-src/hooks/use-attachment-src.ts \
     -o 'components/assistant-ui/elements/file.tsx' https://r.assistant-ui.com/base/files/file/components/assistant-ui/elements/file.tsx \
     -o 'components/assistant-ui/elements/media-player.tsx' https://r.assistant-ui.com/base/files/elements-media-player/components/assistant-ui/elements/media-player.tsx \
     -o 'components/assistant-ui/elements/surfaces.tsx' https://r.assistant-ui.com/base/files/elements-surfaces/components/assistant-ui/elements/surfaces.tsx \
     -o 'components/assistant-ui/utils/useIsomorphicLayoutEffect.ts' https://r.assistant-ui.com/base/files/elements-surfaces/components/assistant-ui/utils/useIsomorphicLayoutEffect.ts \
     -o 'components/assistant-ui/elements/follow-up-suggestions.aui.tsx' https://r.assistant-ui.com/base/files/follow-up-suggestions/components/assistant-ui/elements/follow-up-suggestions.aui.tsx \
     -o 'components/assistant-ui/elements/image.tsx' https://r.assistant-ui.com/base/files/image/components/assistant-ui/elements/image.tsx \
     -o 'components/assistant-ui/utils/href.ts' https://r.assistant-ui.com/base/files/elements-href/components/assistant-ui/utils/href.ts \
     -o 'components/assistant-ui/elements/markdown-text.tsx' https://r.assistant-ui.com/base/files/markdown-text/components/assistant-ui/elements/markdown-text.tsx \
     -o 'hooks/use-copy-to-clipboard.ts' https://r.assistant-ui.com/base/files/use-copy-to-clipboard/hooks/use-copy-to-clipboard.ts \
     -o 'components/assistant-ui/elements/reasoning.aui.tsx' https://r.assistant-ui.com/base/files/reasoning/components/assistant-ui/elements/reasoning.aui.tsx \
     -o 'components/assistant-ui/elements/reasoning.tsx' https://r.assistant-ui.com/base/files/elements-reasoning/components/assistant-ui/elements/reasoning.tsx \
     -o 'components/assistant-ui/utils/useIsomorphicLayoutEffect.ts' https://r.assistant-ui.com/base/files/elements-reasoning/components/assistant-ui/utils/useIsomorphicLayoutEffect.ts \
     -o 'components/assistant-ui/elements/collapsible-root.tsx' https://r.assistant-ui.com/base/files/elements-collapsible-root/components/assistant-ui/elements/collapsible-root.tsx \
     -o 'components/assistant-ui/elements/tool-fallback.aui.tsx' https://r.assistant-ui.com/base/files/tool-fallback/components/assistant-ui/elements/tool-fallback.aui.tsx \
     -o 'components/assistant-ui/elements/tool-group.aui.tsx' https://r.assistant-ui.com/base/files/tool-group/components/assistant-ui/elements/tool-group.aui.tsx
   ```

2. ### Setup Backend Endpoint

   Install provider SDK:

   Choose one:

   **OpenAI**

   ```bash
   npm install ai @assistant-ui/ai-sdk @ai-sdk/openai
   ```

   **Anthropic**

   ```bash
   npm install ai @assistant-ui/ai-sdk @ai-sdk/anthropic
   ```

   **Azure**

   ```bash
   npm install ai @assistant-ui/ai-sdk @ai-sdk/azure
   ```

   **AWS**

   ```bash
   npm install ai @assistant-ui/ai-sdk @ai-sdk/amazon-bedrock
   ```

   **Gemini**

   ```bash
   npm install ai @assistant-ui/ai-sdk @ai-sdk/google
   ```

   **GCP**

   ```bash
   npm install ai @assistant-ui/ai-sdk @ai-sdk/google-vertex
   ```

   **Groq**

   ```bash
   npm install ai @assistant-ui/ai-sdk @ai-sdk/groq
   ```

   **Fireworks**

   ```bash
   npm install ai @assistant-ui/ai-sdk @ai-sdk/fireworks
   ```

   **Cohere**

   ```bash
   npm install ai @assistant-ui/ai-sdk @ai-sdk/cohere
   ```

   **Ollama**

   ```bash
   npm install ai @assistant-ui/ai-sdk ollama-ai-provider-v2
   ```

   **Chrome AI**

   ```bash
   npm install ai @assistant-ui/ai-sdk chrome-ai
   ```

   Add an API endpoint:

   Choose one:

   **OpenAI**

   ```
   import { openai } from "@ai-sdk/openai";
   import { frontendTools } from "@assistant-ui/ai-sdk";
   import { convertToModelMessages, streamText } from "ai";

   export const maxDuration = 30;

   export async function POST(req: Request) {
     const { messages, system, tools } = await req.json();
     const aiSDKTools = frontendTools(tools);
     const result = streamText({
       abortSignal: req.signal,
       model: openai("gpt-6-luna"),
       system,
       messages: await convertToModelMessages(messages, { tools: aiSDKTools }),
       tools: aiSDKTools,
     });
     return result.toUIMessageStreamResponse();
   }
   ```

   **Anthropic**

   ```
   import { anthropic } from "@ai-sdk/anthropic";
   import { frontendTools } from "@assistant-ui/ai-sdk";
   import { convertToModelMessages, streamText } from "ai";

   export const maxDuration = 30;

   export async function POST(req: Request) {
     const { messages, system, tools } = await req.json();
     const aiSDKTools = frontendTools(tools);
     const result = streamText({
       abortSignal: req.signal,
       model: anthropic("claude-sonnet-5"),
       system,
       messages: await convertToModelMessages(messages, { tools: aiSDKTools }),
       tools: aiSDKTools,
     });
     return result.toUIMessageStreamResponse();
   }
   ```

   **Azure**

   ```
   import { azure } from "@ai-sdk/azure";
   import { frontendTools } from "@assistant-ui/ai-sdk";
   import { convertToModelMessages, streamText } from "ai";

   export const maxDuration = 30;

   export async function POST(req: Request) {
     const { messages, system, tools } = await req.json();
     const aiSDKTools = frontendTools(tools);
     const result = streamText({
       abortSignal: req.signal,
       model: azure("your-deployment-name"),
       system,
       messages: await convertToModelMessages(messages, { tools: aiSDKTools }),
       tools: aiSDKTools,
     });
     return result.toUIMessageStreamResponse();
   }
   ```

   **AWS**

   ```
   import { bedrock } from "@ai-sdk/amazon-bedrock";
   import { frontendTools } from "@assistant-ui/ai-sdk";
   import { convertToModelMessages, streamText } from "ai";

   export const maxDuration = 30;

   export async function POST(req: Request) {
     const { messages, system, tools } = await req.json();
     const aiSDKTools = frontendTools(tools);
     const result = streamText({
       abortSignal: req.signal,
       model: bedrock("us.anthropic.claude-sonnet-5"),
       system,
       messages: await convertToModelMessages(messages, { tools: aiSDKTools }),
       tools: aiSDKTools,
     });
     return result.toUIMessageStreamResponse();
   }
   ```

   **Gemini**

   ```
   import { google } from "@ai-sdk/google";
   import { frontendTools } from "@assistant-ui/ai-sdk";
   import { convertToModelMessages, streamText } from "ai";

   export const maxDuration = 30;

   export async function POST(req: Request) {
     const { messages, system, tools } = await req.json();
     const aiSDKTools = frontendTools(tools);
     const result = streamText({
       abortSignal: req.signal,
       model: google("gemini-3.8-flash"),
       system,
       messages: await convertToModelMessages(messages, { tools: aiSDKTools }),
       tools: aiSDKTools,
     });
     return result.toUIMessageStreamResponse();
   }
   ```

   **GCP**

   ```
   import { vertex } from "@ai-sdk/google-vertex";
   import { frontendTools } from "@assistant-ui/ai-sdk";
   import { convertToModelMessages, streamText } from "ai";

   export const maxDuration = 30;

   export async function POST(req: Request) {
     const { messages, system, tools } = await req.json();
     const aiSDKTools = frontendTools(tools);
     const result = streamText({
       abortSignal: req.signal,
       model: vertex("gemini-3.8-flash"),
       system,
       messages: await convertToModelMessages(messages, { tools: aiSDKTools }),
       tools: aiSDKTools,
     });
     return result.toUIMessageStreamResponse();
   }
   ```

   **Groq**

   ```
   import { groq } from "@ai-sdk/groq";
   import { frontendTools } from "@assistant-ui/ai-sdk";
   import { convertToModelMessages, streamText } from "ai";

   export const maxDuration = 30;

   export async function POST(req: Request) {
     const { messages, system, tools } = await req.json();
     const aiSDKTools = frontendTools(tools);
     const result = streamText({
       abortSignal: req.signal,
       model: groq("openai/gpt-oss-120b"),
       system,
       messages: await convertToModelMessages(messages, { tools: aiSDKTools }),
       tools: aiSDKTools,
     });
     return result.toUIMessageStreamResponse();
   }
   ```

   **Fireworks**

   ```
   import { fireworks } from "@ai-sdk/fireworks";
   import { frontendTools } from "@assistant-ui/ai-sdk";
   import { convertToModelMessages, streamText } from "ai";

   export const maxDuration = 30;

   export async function POST(req: Request) {
     const { messages, system, tools } = await req.json();
     const aiSDKTools = frontendTools(tools);
     const result = streamText({
       abortSignal: req.signal,
       model: fireworks("accounts/fireworks/models/llama-v3p3-70b-instruct"),
       system,
       messages: await convertToModelMessages(messages, { tools: aiSDKTools }),
       tools: aiSDKTools,
     });
     return result.toUIMessageStreamResponse();
   }
   ```

   **Cohere**

   ```
   import { cohere } from "@ai-sdk/cohere";
   import { frontendTools } from "@assistant-ui/ai-sdk";
   import { convertToModelMessages, streamText } from "ai";

   export const maxDuration = 30;

   export async function POST(req: Request) {
     const { messages, system, tools } = await req.json();
     const aiSDKTools = frontendTools(tools);
     const result = streamText({
       abortSignal: req.signal,
       model: cohere("command-r-plus"),
       system,
       messages: await convertToModelMessages(messages, { tools: aiSDKTools }),
       tools: aiSDKTools,
     });
     return result.toUIMessageStreamResponse();
   }
   ```

   **Ollama**

   ```
   import { ollama } from "ollama-ai-provider-v2";
   import { frontendTools } from "@assistant-ui/ai-sdk";
   import { convertToModelMessages, streamText } from "ai";

   export const maxDuration = 30;

   export async function POST(req: Request) {
     const { messages, system, tools } = await req.json();
     const aiSDKTools = frontendTools(tools);
     const result = streamText({
       abortSignal: req.signal,
       model: ollama("gpt-oss:20b"),
       system,
       messages: await convertToModelMessages(messages, { tools: aiSDKTools }),
       tools: aiSDKTools,
     });
     return result.toUIMessageStreamResponse();
   }
   ```

   **Chrome AI**

   ```
   import { chromeai } from "chrome-ai";
   import { frontendTools } from "@assistant-ui/ai-sdk";
   import { convertToModelMessages, streamText } from "ai";

   export const maxDuration = 30;

   export async function POST(req: Request) {
     const { messages, system, tools } = await req.json();
     const aiSDKTools = frontendTools(tools);
     const result = streamText({
       abortSignal: req.signal,
       model: chromeai(),
       system,
       messages: await convertToModelMessages(messages, { tools: aiSDKTools }),
       tools: aiSDKTools,
     });
     return result.toUIMessageStreamResponse();
   }
   ```

   The `system` prompt and the `tools` schemas in the request body come from the browser, so any caller can change them. Treat both as untrusted input. A system prompt only guides the model, so enforce authorization, argument validation and usage limits in server code, define tools the server executes on the server rather than from the request, and authenticate the route before deploying.

   Define environment variables:

   Choose one:

   **OpenAI**

   ```
   OPENAI_API_KEY="sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   ```

   **Anthropic**

   ```
   ANTHROPIC_API_KEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   ```

   **Azure**

   ```
   AZURE_RESOURCE_NAME="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   AZURE_API_KEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   ```

   **AWS**

   ```
   AWS_ACCESS_KEY_ID="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   AWS_SECRET_ACCESS_KEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   AWS_REGION="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   ```

   **Gemini**

   ```
   GOOGLE_GENERATIVE_AI_API_KEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   ```

   **GCP**

   ```
   GOOGLE_VERTEX_PROJECT="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   GOOGLE_VERTEX_LOCATION="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   GOOGLE_APPLICATION_CREDENTIALS="/path/to/service-account.json"
   ```

   **Groq**

   ```
   GROQ_API_KEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   ```

   **Fireworks**

   ```
   FIREWORKS_API_KEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   ```

   **Cohere**

   ```
   COHERE_API_KEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
   ```

   **Ollama**

   ```
   <none>
   ```

   **Chrome AI**

   ```
   <none>
   ```

   If you aren't using Next.js, you can also deploy this endpoint to Cloudflare Workers, or any other serverless platform.

3. ### Use it in your app

   Choose one:

   **Thread**

   ```
   "use client";

   import { AssistantRuntimeProvider } from "@assistant-ui/react";
   import { useChatRuntime, AssistantChatTransport } from "@assistant-ui/ai-sdk";
   import { ThreadList } from "@/components/assistant-ui/elements/thread-list.aui";
   import { Thread } from "@/components/assistant-ui/elements/thread.aui";

   export default function MyApp() {
     const runtime = useChatRuntime({
       transport: new AssistantChatTransport({
         api: "/api/chat",
       }),
     });

     return (
       <AssistantRuntimeProvider runtime={runtime}>
         <div>
           <ThreadList />
           <Thread />
         </div>
       </AssistantRuntimeProvider>
     );
   }
   ```

   **AssistantModal**

   ```
   "use client";

   // run `npx shadcn@latest add @assistant-ui/assistant-modal`

   import { AssistantRuntimeProvider } from "@assistant-ui/react";
   import { useChatRuntime, AssistantChatTransport } from "@assistant-ui/ai-sdk";
   import { AssistantModal } from "@/components/assistant-ui/elements/assistant-modal.aui";

   export default function MyApp() {
     const runtime = useChatRuntime({
       transport: new AssistantChatTransport({
         api: "/api/chat",
       }),
     });

     return (
       <AssistantRuntimeProvider runtime={runtime}>
         <AssistantModal />
       </AssistantRuntimeProvider>
     );
   }
   ```

## What's Next?

- [Pick a Runtime](/docs/runtimes/pick-a-runtime) — Choose the right runtime for your needs
- [Generative UI](/docs/tools/generative-ui) — Let the model compose UI from a component vocabulary you ship
- [Add Persistence](/docs/cloud) — Save and restore chat conversations
- [Examples](https://github.com/assistant-ui/assistant-ui/tree/main/examples) — Explore full implementations and demos