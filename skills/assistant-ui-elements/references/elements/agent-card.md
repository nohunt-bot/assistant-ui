# Agent card  (`agent-card`)

Who you are about to talk to: its skills, its model, and the endpoint behind it.

- 完整文件：`references/pages/agent-card.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-agent-card.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-agent-card.md`

## With a runtime

`@assistant-ui/react-a2a` fetches the remote agent's card as part of connecting to it, and exposes it through a read-only hook.

```tsx
"use client";

import { AssistantRuntimeProvider } from "@assistant-ui/react";
import { useA2ARuntime } from "@assistant-ui/react-a2a";

export function A2AProvider({ children }: { children: React.ReactNode }) {
  const runtime = useA2ARuntime({ baseUrl: "https://weather-agent.example.com" });
  return (
    <AssistantRuntimeProvider runtime={runtime}>
      {children}
    </AssistantRuntimeProvider>
  );
}

```

## Standalone (no runtime)

Standalone, every field is a prop; nothing is fetched for you.

```tsx
import { AgentCard } from "@/components/assistant-ui/elements/agent-card";

export default function AgentPage() {
  return (
    <AgentCard
      name="Weather Agent"
      description="Answers questions about current and forecast weather."
      provider="Acme Corp"
      version="1.4.0"
      model="gpt-6-astra"
      endpoint="weather-agent.example.com"
      skills={[
        { name: "get_forecast", description: "5-day forecast for a location" },
        { name: "get_alerts", description: "Active severe weather alerts" },
      ]}
      connected={false}
      onConnect={() => connectToAgent("weather-agent")}
    />
  );
}

```
