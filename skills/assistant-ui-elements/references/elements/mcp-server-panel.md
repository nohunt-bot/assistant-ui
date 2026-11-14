# Server panel  (`mcp-server-panel`)

Which servers are connected, what each one brought, and which is still waiting on you.

- 完整文件：`references/pages/mcp-server-panel.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-mcp-server-panel.md`

## With a runtime

`@assistant-ui/react-mcp` owns MCP connection state. Mount its manager once, then read servers and their tools from it anywhere in the tree.

```tsx
"use client";

import { AuiConfig, AuiProvider, useAui } from "@assistant-ui/store";
import { McpManagerResource, type MCPConnector } from "@assistant-ui/react-mcp";

function McpManagerMount({
  connectors,
  children,
}: {
  connectors: MCPConnector[];
  children: React.ReactNode;
}) {
  const aui = useAui();
  const config = AuiConfig({ mcp: McpManagerResource({ connectors }) });
  return (
    <AuiProvider extends={aui} config={config}>
      {children}
    </AuiProvider>
  );
}

```

## Standalone (no runtime)

Standalone, the panel is a controlled list: you own the servers, their status, and which row is expanded.

```tsx
"use client";

import { useState } from "react";
import { McpServerPanel, type McpServer } from "@/components/assistant-ui/elements/mcp-server-panel";

const servers: McpServer[] = [
  { id: "files", name: "Filesystem", transport: "stdio", status: "connected", tools: ["read_file", "write_file"] },
  { id: "search", name: "Web search", transport: "http", status: "needs-auth", tools: ["search"] },
];

export function Servers() {
  const [expandedId, setExpandedId] = useState<string>();
  return (
    <McpServerPanel
      servers={servers}
      expandedId={expandedId}
      onToggle={(id) => setExpandedId((current) => (current === id ? undefined : id))}
    />
  );
}

```
