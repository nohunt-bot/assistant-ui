# Assistant Frame
URL: /docs/api-reference/transport/frame

Frame bridge APIs and serialized message types for embedding assistant-ui runtimes in external contexts.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### AssistantFrameHost

- `constructor?`: `(iframeWindow: Window, targetOrigin: string = getDefaultTargetOrigin()) => AssistantFrameHost`
- `frameLoaded?`: `() => void` — Request tools after each iframe load; clear the previous document if no provider answers within two seconds.
- `getModelContext?`: `() => ModelContext`
- `subscribe?`: `(callback: () => void) => Unsubscribe`
- `dispose?`: `() => void`

### AssistantFrameProvider

- `static addModelContextProvider?`: `(provider: ModelContextProvider, targetOrigin?: string) => Unsubscribe`
- `static dispose?`: `() => void`

### FRAME_MESSAGE_CHANNEL

```
const FRAME_MESSAGE_CHANNEL: "assistant-ui-frame";
```

### FrameMessage

- `type`: `"model-context-request"`
- `documentId?`: `string`

### SerializedModelContext

- `system?`: `string`
- `tools?`: `Record<string, SerializedTool>`

### SerializedTool

- `description?`: `string`
- `parameters`: `any`
- `disabled?`: `boolean`
- `type?`: `string`

### useAssistantFrameHost

React hook that manages the lifecycle of an AssistantFrameHost and its binding to the current AssistantRuntime.

Usage example:

```
function MyComponent() {
  const iframeRef = useRef<HTMLIFrameElement>(null);

  useAssistantFrameHost({
    iframeRef,
    targetOrigin: "https://trusted-domain.com",
  });

  return <iframe ref={iframeRef} src="..." />;
}
```

- `options`: `UseAssistantFrameHostOptions`

  - `iframeRef`: `Readonly<RefObject<HTMLIFrameElement | null | undefined>>`
  - `targetOrigin?`: `string`
  - `register`: `(frameHost: AssistantFrameHost) => Unsubscribe`