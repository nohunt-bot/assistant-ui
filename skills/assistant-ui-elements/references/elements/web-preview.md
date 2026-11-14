# Web preview  (`web-preview`)

Chrome for a sandboxed preview: a URL bar, reload, and open-in-new around a frame you isolate.

- 完整文件：`references/pages/web-preview.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-web-preview.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-web-preview.md`

## With a runtime

`WebPreview` never sandboxes its `children` itself, so pairing it with a tool that returns model-written markup means isolating that markup yourself. [`safe-content-frame`](/safe-content-frame) is built for this: it mounts an iframe on a separate, per-render origin so model-generated script cannot reach your page's cookies, storage, or DOM.

```tsx
"use client";

import { useEffect, useRef, useState } from "react";
import { SafeContentFrame, type RenderedFrame } from "safe-content-frame";
import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import { WebPreview } from "@/components/assistant-ui/elements/web-preview";

const frame = new SafeContentFrame("my-app");

export const RenderPreviewToolUI: ToolCallMessagePartComponent<
  Record<string, never>,
  { html: string }
> = ({ result }) => {
  const containerRef = useRef<HTMLDivElement>(null);
  const renderedRef = useRef<RenderedFrame | undefined>(undefined);
  const [origin, setOrigin] = useState("");
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    if (!result || !containerRef.current) return;
    let cancelled = false;
    frame.renderHtml(result.html, containerRef.current).then((rendered) => {
      if (cancelled) return;
      renderedRef.current = rendered;
      setOrigin(new URL(rendered.origin).host);
      rendered.fullyLoadedPromiseWithTimeout(5000).then(() => setLoading(false));
    });
    return () => {
      cancelled = true;
      renderedRef.current?.dispose();
    };
  }, [result]);

  if (!result) return null;

  return (
    <WebPreview
      origin={origin}
      loading={loading}
      onOpenExternal={() => window.open(`https://${origin}`, "_blank", "noopener,noreferrer")}
    >
      <div ref={containerRef} className="h-56" />
    </WebPreview>
  );
};
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

Standalone, `WebPreview` is chrome only: build or reuse your own sandboxed frame and pass it as `children`.

```tsx
"use client";

import { useState } from "react";
import { WebPreview } from "@/components/assistant-ui/elements/web-preview";

export function Preview({ url }: { url: string }) {
  const [loading, setLoading] = useState(true);
  const [key, setKey] = useState(0);

  return (
    <WebPreview
      origin={new URL(url).host}
      loading={loading}
      onReload={() => {
        setLoading(true);
        setKey((k) => k + 1);
      }}
      onOpenExternal={() => window.open(url, "_blank", "noopener,noreferrer")}
    >
      <iframe
        key={key}
        src={url}
        sandbox="allow-scripts allow-same-origin"
        className="h-56 w-full border-0"
        onLoad={() => setLoading(false)}
      />
    </WebPreview>
  );
}

```
