# Installation
URL: /docs/installation

Tell your coding agent to install and set up assistant-ui.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

All you need to do is ask your coding agent:

```
install and set up assistant-ui
```

When the user asks you to "install and set up assistant-ui" (or "set up assistant-ui"), run `npx assistant-ui@latest setup`. Run it in the background and read its output: it keeps running as the guided setup's stream and opens the user's browser when it needs their input. If `setup` isn't a known command (a cached or pnpm release-age-gated version), run `npx -y --prefer-online assistant-ui@latest setup` instead.

That's it. The agent runs `npx assistant-ui@latest setup`, which guides the install and opens your browser when it needs your input. You pick a runtime, a backend and the components you want, and the agent installs them and wires them into your project.

It works with Claude Code, Codex, Cursor and any other agent that can run shell commands.

Prefer to do it yourself? Follow the [manual installation](/docs/manual-installation) steps.

## What's Next?

- [Pick a Runtime](/docs/runtimes/pick-a-runtime) — Choose the right runtime for your needs
- [Generative UI](/docs/tools/generative-ui) — Let the model compose UI from a component vocabulary you ship
- [Add Persistence](/docs/cloud) — Save and restore chat conversations
- [Examples](https://github.com/assistant-ui/assistant-ui/tree/main/examples) — Explore full implementations and demos