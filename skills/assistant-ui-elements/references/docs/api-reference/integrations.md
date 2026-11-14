# Integrations API Reference
URL: /docs/api-reference/integrations

Package-level APIs for connecting assistant-ui React to the Vercel AI SDK, Assistant Cloud, and adjacent chat ecosystem hooks and runtimes.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

Integration packages connect assistant-ui to adjacent React and AI ecosystems. Use this section when you need the exact hooks, options, and helper exports for a package such as `@assistant-ui/ai-sdk`, `@assistant-ui/react-data-stream`, or `@assistant-ui/eve`.

## Pages

- [@assistant-ui/ai-sdk](/docs/api-reference/integrations/ai-sdk) — Vercel AI SDK runtime hooks, chat transports, and message conversion utilities for assistant-ui applications.
- [@assistant-ui/react-data-stream](/docs/api-reference/integrations/react-data-stream) — Data stream runtime hook and message conversion utilities for custom assistant-ui streaming backends.
- [@assistant-ui/eve](/docs/api-reference/integrations/eve) — Eve runtime hook and message conversion utilities for assistant-ui React applications.
- [assistant-cloud](/docs/api-reference/integrations/assistant-cloud) — The assistant-cloud client for threads, messages, runs, events, scores and files, with the reporters, persistence and run report helpers an integration is built from.
- [assistant-cloud/ai-sdk](/docs/api-reference/integrations/assistant-cloud-ai-sdk) — The AI SDK message format adapter and run telemetry extraction that the assistant-ui runtime uses, typed against UIMessage without loading the ai runtime.
- [assistant-cloud/telemetry](/docs/api-reference/integrations/assistant-cloud-telemetry) — OpenTelemetry exporter, span processor and stream metadata helpers that send a server's GenAI spans to Assistant Cloud and hand the trace id to the browser.