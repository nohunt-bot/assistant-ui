# MCP config dialog  (`mcp-config`)

A dialog for connectors and custom MCP servers, including authentication and connection state.

- 完整文件：`references/pages/mcp-config.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/mcp-config.md`
- 安裝（standalone）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔

## With a runtime

`McpManagerResource` is the entry point: mount it once at the root of your app, with the connectors you want available to every user.

```tsx
import { AuiConfig, AuiProvider, useAui } from "@assistant-ui/react";
import { McpManagerResource } from "@assistant-ui/react-mcp";

export function McpProviders({ children }: { children: React.ReactNode }) {
  const aui = useAui();
  const config = AuiConfig({
    mcp: McpManagerResource({
      connectors: [
        { id: "linear", name: "Linear", url: "https://mcp.linear.app/mcp", auth: { type: "oauth" } },
      ],
    }),
  });
  return (
    <AuiProvider extends={aui} config={config}>
      {children}
    </AuiProvider>
  );
}

```

## Standalone (no runtime)

Standalone, there's no connection to configure. `McpConfigDialog` drives a live `McpManagerResource`, so it takes no static props for servers or their state. For a single server card without the dialog chrome, compose `McpServerPrimitive.Root` against a mounted manager. For a server list driven by props, see [Server panel](/elements/mcp-server-panel).
