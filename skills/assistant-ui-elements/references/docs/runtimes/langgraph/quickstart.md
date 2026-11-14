# Quickstart
URL: /docs/runtimes/langgraph/quickstart

From-template and manual setup paths to a working LangGraph chat.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

Two paths to a running chat against a LangGraph Cloud server. The template is fastest; the manual path is what you adapt when integrating into an existing project.

## From the template

**React**

```
npx create-assistant-ui@latest -t langchain my-app
cd my-app
```

Set environment variables:

```
# LANGCHAIN_API_KEY=your_api_key       # production
# LANGGRAPH_API_URL=your_api_url       # production
NEXT_PUBLIC_LANGGRAPH_API_URL=your_api_url           # development (no API key required)
NEXT_PUBLIC_LANGGRAPH_ASSISTANT_ID=your_graph_id
```

```
npm run dev:frontend
```

`npm run dev` also starts the template's bundled agent with `langgraphjs dev`, which needs `ANTHROPIC_API_KEY`.

The template connects to LangGraph through `useStreamRuntime` from [`@assistant-ui/react-langchain`](/docs/runtimes/langchain); continue on that page to add features. To use `useLangGraphRuntime`, follow the manual setup below.

## Manual setup in an existing project

1. ### Install dependencies

   **React**

   ```bash
   npm install @assistant-ui/react @assistant-ui/react-langgraph @langchain/langgraph-sdk
   ```

2. ### Create the LangGraph client helper

   **React**

   ```
   import { Client } from "@langchain/langgraph-sdk";

   export const createClient = () => {
     const apiUrl =
       process.env["NEXT_PUBLIC_LANGGRAPH_API_URL"] ||
       (typeof window !== "undefined"
         ? new URL("/api", window.location.href).href
         : "/api");
     return new Client({ apiUrl });
   };
   ```

3. ### Build the assistant component

   **React**

   ```
   "use client";

   import { useMemo } from "react";
   import { Thread } from "@/components/assistant-ui/elements/thread.aui";
   import { AssistantRuntimeProvider } from "@assistant-ui/react";
   import {
     unstable_createLangGraphStream,
     useLangGraphRuntime,
     type LangChainMessage,
   } from "@assistant-ui/react-langgraph";

   import { createClient } from "@/lib/chatApi";

   const ASSISTANT_ID = process.env["NEXT_PUBLIC_LANGGRAPH_ASSISTANT_ID"]!;

   export function MyAssistant() {
     const client = useMemo(() => createClient(), []);
     const stream = useMemo(
       () =>
         unstable_createLangGraphStream({
           client,
           assistantId: ASSISTANT_ID,
         }),
       [client],
     );

     const runtime = useLangGraphRuntime({
       unstable_allowCancellation: true,
       stream,
       create: async () => {
         const { thread_id } = await client.threads.create();
         return { externalId: thread_id };
       },
       load: async (externalId) => {
         const state = await client.threads.getState<{
           messages: LangChainMessage[];
         }>(externalId);
         return {
           messages: state.values.messages,
           interrupts: state.tasks[0]?.interrupts,
         };
       },
     });

     return (
       <AssistantRuntimeProvider runtime={runtime}>
         <Thread />
       </AssistantRuntimeProvider>
     );
   }
   ```

4. ### Mount the component

   **React**

   ```
   import { MyAssistant } from "@/components/MyAssistant";

   export default function Home() {
     return (
       <main className="h-dvh">
         <MyAssistant />
       </main>
     );
   }
   ```

5. ### Set environment variables

   **React**

   Use the same `.env.local` shape as the template path [above](#from-the-template).

6. ### Set up UI components

   **React**

   Follow the [UI Components guide](/elements/thread) to wire up the Thread, composer, and supporting primitives.

## Production proxy backend

For development, the client above hits LangGraph Cloud directly using `NEXT_PUBLIC_LANGGRAPH_API_URL`. For production, proxy through your own backend so your API key never reaches the client. Limit the proxy to the endpoints you actually need.

```
import { type NextRequest, NextResponse } from "next/server";

function isAllowedPath(path: string, method: string) {
  const thread = "[a-zA-Z0-9_-]+";
  if (method === "GET")
    return new RegExp(`^threads/${thread}/state$`).test(path);
  if (method !== "POST") return false;
  return (
    path === "threads" ||
    new RegExp(
      `^threads/${thread}/(?:commands|stream/events|history|runs/stream|runs/${thread}/cancel)$`,
    ).test(path)
  );
}

function isAllowedRequestContext(req: NextRequest) {
  const fetchSite = req.headers.get("sec-fetch-site");
  if (fetchSite !== null) {
    return fetchSite === "same-origin" || fetchSite === "none";
  }

  const origin = req.headers.get("origin");
  if (origin === null) return true;

  try {
    return new URL(origin).origin === req.nextUrl.origin;
  } catch {
    return false;
  }
}

function crossOriginResponse() {
  return NextResponse.json(
    { error: "Cross-origin requests are not allowed." },
    { status: 403 },
  );
}

async function handleRequest(req: NextRequest, method: string) {
  if (!isAllowedRequestContext(req)) return crossOriginResponse();

  try {
    const path = req.nextUrl.pathname.replace(/^\/?api\//, "");
    if (!isAllowedPath(path, method)) {
      return new NextResponse(null, { status: 404 });
    }

    const apiUrl = process.env.LANGGRAPH_API_URL?.trim();
    if (!apiUrl) {
      return NextResponse.json(
        { error: "LANGGRAPH_API_URL is not configured." },
        { status: 503 },
      );
    }

    const searchParams = new URLSearchParams(req.nextUrl.search);
    searchParams.delete("_path");
    searchParams.delete("nxtP_path");
    const queryString = searchParams.toString()
      ? `?${searchParams.toString()}`
      : "";

    const headers = new Headers();
    const apiKey = process.env.LANGCHAIN_API_KEY?.trim();
    if (apiKey) headers.set("x-api-key", apiKey);

    const accept = req.headers.get("accept");
    if (accept) headers.set("accept", accept);

    const contentType = req.headers.get("content-type");
    if (contentType) headers.set("content-type", contentType);

    const options: RequestInit = {
      method,
      headers,
      redirect: "manual",
      signal: req.signal,
    };

    if (["POST", "PUT", "PATCH"].includes(method)) {
      options.body = await req.text();
    }

    const res = await fetch(`${apiUrl}/${path}${queryString}`, options);

    if (
      res.status === 0 ||
      (res.status >= 300 && res.status < 400 && res.status !== 304) ||
      res.type === "opaqueredirect"
    ) {
      await res.body?.cancel().catch(() => undefined);
      return NextResponse.json(
        { error: "LangGraph returned an unexpected redirect." },
        { status: 502 },
      );
    }

    const responseHeaders = new Headers(res.headers);
    for (const name of [
      "access-control-allow-credentials",
      "access-control-allow-headers",
      "access-control-allow-methods",
      "access-control-allow-origin",
      "content-encoding",
      "content-length",
      "set-cookie",
      "transfer-encoding",
    ]) {
      responseHeaders.delete(name);
    }
    responseHeaders.set("Cache-Control", "no-store");

    return new NextResponse(res.body, {
      status: res.status,
      statusText: res.statusText,
      headers: responseHeaders,
    });
  } catch (e: unknown) {
    if (e instanceof Error) {
      const typedError = e as Error & { status?: number };
      return NextResponse.json(
        { error: typedError.message },
        { status: typedError.status ?? 500 },
      );
    }
    return NextResponse.json({ error: "Unknown error" }, { status: 500 });
  }
}

export const GET = (req: NextRequest) => handleRequest(req, "GET");
export const HEAD = () => new NextResponse(null, { status: 404 });
export const POST = (req: NextRequest) => handleRequest(req, "POST");
export const PUT = () => new NextResponse(null, { status: 404 });
export const PATCH = () => new NextResponse(null, { status: 404 });
export const DELETE = () => new NextResponse(null, { status: 404 });
export const OPTIONS = (req: NextRequest) =>
  isAllowedRequestContext(req)
    ? new NextResponse(null, { status: 404 })
    : crossOriginResponse();
```

The route forwards only the paths `useStreamRuntime` and `useLangGraphRuntime` send (creating a thread, reading its state and history, commands, stream events, run streams and cancellation) and answers anything else with 404. It accepts a request only when `Sec-Fetch-Site` is `same-origin` or `none`, falls back to comparing the `Origin` header against the request URL for clients that omit Fetch Metadata, and does not follow upstream redirects. Behind a reverse proxy, that fallback needs the public scheme and host preserved in the request URL.

> [!warn]
>
> A request-context check is not authentication: requests that send neither header still pass. Before deploying with a cloud API key, require your application session in the proxy route, check that the signed-in user owns each thread it forwards, and apply a durable rate limit.

With this route in place, drop `NEXT_PUBLIC_LANGGRAPH_API_URL` from production env vars; the client helper falls back to the same-origin `/api` path. Set `LANGCHAIN_API_KEY` and `LANGGRAPH_API_URL` server-side instead.

## Next

- [Streaming](/docs/runtimes/langgraph/streaming) — Event handlers, message metadata, message conversion.
- [Generative UI](/docs/runtimes/langgraph/generative-ui) — Structured UI components emitted by your graph.
- [Interrupts](/docs/runtimes/langgraph/interrupts) — Interrupt persistence and checkpoint-based message editing.