# Generative UI（`aui/generative-ui`，base）

Styled component library for rendering generative UI output.

- 直接依賴：`aui/generative-ui-style`, `aui/elements-generative-markdown`, `aui/elements-mermaid-diagram`, `aui/elements-geo-map`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/generative-ui-style`, `aui/elements-generative-markdown`, `aui/elements-mermaid-diagram`, `aui/elements-surfaces`, `aui/elements-geo-map`, `aui/generative-ui`
- npm（整組）：`npm install cn react-markdown remark-gfm beautiful-mermaid lucide-react tw-shimmer@^0.4.14 leaflet @assistant-ui/generative-ui@^0.0.11 zod`
- npm dev（整組）：`npm install -D @types/leaflet`

## 檔案：`components/assistant-ui/elements/generative-ui.tsx`

```tsx
"use client";

import { Component, lazy, Suspense, type ReactNode } from "react";
import type { GenerativeUILibrary } from "@assistant-ui/generative-ui/react";
import { defaultGenerativeUILibrary } from "@assistant-ui/generative-ui/react";
import { GenerativeMarkdown } from "./generative-markdown";
import { GeoMap, type GeoMapPlace } from "./geo-map";

// The diagram engine (beautiful-mermaid and elkjs) is large, so it loads only
// when a Diagram actually renders. The fallback matches the diagram's own
// rendering skeleton, so nothing shifts when the chunk arrives. Server
// rendering still outputs the finished diagram through the Suspense boundary.
const MermaidDiagram = lazy(() =>
  import("./mermaid-diagram").then((mod) => ({ default: mod.MermaidDiagram })),
);

const DiagramSkeleton = () => (
  <div
    data-slot="mermaid-skeleton"
    aria-label="Rendering diagram"
    aria-busy="true"
    className="aui-mermaid-skeleton bg-muted flex h-32 animate-pulse items-center justify-center gap-3 rounded-b-lg p-4"
  >
    <div className="bg-muted-foreground/20 h-8 w-20 rounded-md" />
    <div className="bg-muted-foreground/20 h-px w-10" />
    <div className="bg-muted-foreground/20 h-8 w-20 rounded-md" />
    <div className="bg-muted-foreground/20 h-px w-10" />
    <div className="bg-muted-foreground/20 h-8 w-20 rounded-md" />
  </div>
);

const markdownBase = defaultGenerativeUILibrary.Markdown!;

const pinsOf = (places: unknown): GeoMapPlace[] =>
  (Array.isArray(places) ? places : []).flatMap((place: unknown, index) => {
    if (place === null || typeof place !== "object") return [];
    const { label, lat, lng, detail } = place as Record<string, unknown>;
    return typeof label === "string" &&
      typeof lat === "number" &&
      typeof lng === "number"
      ? [
          {
            id: String(index),
            label,
            lat,
            lng,
            description: typeof detail === "string" ? detail : undefined,
          },
        ]
      : [];
  });

// If the diagram chunk fails to load, show the source instead of throwing.
class DiagramBoundary extends Component<
  { code: string; children: ReactNode },
  { failed: boolean }
> {
  override state = { failed: false };

  static getDerivedStateFromError() {
    return { failed: true };
  }

  override render() {
    if (!this.state.failed) return this.props.children;
    return (
      <div
        data-slot="mermaid-fallback"
        className="aui-mermaid-fallback bg-muted/75 rounded-b-lg"
      >
        <pre className="overflow-x-auto p-4 text-sm">
          {this.props.code.trim()}
        </pre>
      </div>
    );
  }
}

/** `MarkdownTextPrimitive` cannot be reused here: it reads from message-part context, not a prop string. */
export const styledGenerativeUILibrary: GenerativeUILibrary = {
  ...defaultGenerativeUILibrary,
  Markdown: {
    properties: markdownBase.properties,
    streamProperties: markdownBase.streamProperties,
    description: "A markdown string, rendered with GitHub-flavored markdown.",
    render: GenerativeMarkdown,
  },
  Diagram: {
    ...defaultGenerativeUILibrary.Diagram!,
    render: ({ code, title }) => (
      <figure data-aui="diagram">
        <DiagramBoundary code={typeof code === "string" ? code : ""}>
          <Suspense fallback={<DiagramSkeleton />}>
            <MermaidDiagram code={typeof code === "string" ? code : ""} />
          </Suspense>
        </DiagramBoundary>
        {typeof title === "string" || typeof title === "number" ? (
          <figcaption data-aui="diagram-title">{title}</figcaption>
        ) : null}
      </figure>
    ),
  },
  Map: {
    ...defaultGenerativeUILibrary.Map!,
    render: ({ places, route }) => {
      const pins = pinsOf(places);
      return (
        <GeoMap
          data-aui="map"
          className="max-w-none [&_[data-slot=geo-map-coordinates]]:hidden"
          places={pins}
          routes={
            route && pins.length > 1
              ? [
                  {
                    id: "route",
                    points: pins.map(({ lat, lng }) => [lat, lng] as const),
                  },
                ]
              : undefined
          }
        />
      );
    },
  },
};
```
