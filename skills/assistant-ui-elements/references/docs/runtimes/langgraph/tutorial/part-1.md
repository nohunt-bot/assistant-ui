# Part 1: Setup frontend
URL: /docs/runtimes/langgraph/tutorial/part-1

Create a Next.js project with the LangGraph assistant-ui template.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## Create a new project

Run the following command to create a new Next.js project with the LangGraph assistant-ui template:

```
npx create-assistant-ui@latest -t langchain my-app
cd my-app
```

Your project includes the following files:

- name

  my-app

* name

  app

- name

  api

* name

  \[...\_path]

- name

  route.ts

* name

  assistant.tsx

- name

  globals.css

* name

  layout.tsx

- name

  page.tsx

* name

  backend

- name

  agent.ts

* name

  components

- name

  assistant-ui

* name

  elements

- name

  thread.aui.tsx

* name

  ui

- name

  button.tsx

* name

  tooltip.tsx

- name

  lib

* name

  utils.ts

- name

  components.json

* name

  langgraph.json

- name

  next.config.ts

* name

  package.json

- name

  postcss.config.mjs

* name

  tsconfig.json

### Setup environment variables

Create a `.env.local` file in your project with the following variables:

```
LANGGRAPH_API_URL=https://assistant-ui-stockbroker.vercel.app/api
NEXT_PUBLIC_LANGGRAPH_ASSISTANT_ID=stockbroker
```

This connects the frontend to a LangGraph Cloud endpoint running under\
`https://assistant-ui-stockbroker.vercel.app/api`.\
This endpoint is running the LangGraph agent defined [in this repository](https://github.com/assistant-ui/assistant-ui-stockbroker/blob/main/backend).

### Start the server

The template also bundles a local LangGraph backend (`backend/agent.ts`), which this tutorial does not use. Start only the frontend:

```
npm run dev:frontend
```

The server will start and you can view the frontend by opening a browser tab to <http://localhost:3000>.

You should be able to chat with the assistant and see LLM responses streaming in real-time.

## Explore features

### Streaming

Streaming message support is enabled by default. The template's runtime, `useStreamRuntime` from `@assistant-ui/react-langchain`, delegates stream handling to `useStream` from `@langchain/react`, which accumulates text and tool call chunks as they arrive.

### Markdown support

Rich text rendering using Markdown is enabled by default.