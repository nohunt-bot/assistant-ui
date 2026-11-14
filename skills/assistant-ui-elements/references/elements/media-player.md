# Media player  (`media-player`)

Compact inline audio and video players for files and standalone media URLs.

- 完整文件：`references/pages/media-player.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-media-player.md`

## With a runtime

`File` turns playable `audio/*` and `video/*` file parts into the matching player automatically. Keep them as file parts so the runtime preserves the same wire shape for every adapter.

```tsx
import { File } from "@/components/assistant-ui/elements/file";
import { MessagePrimitive } from "@assistant-ui/react";

function AssistantMessage() {
  return (
    <MessagePrimitive.Root>
      <MessagePrimitive.Parts components={{ File }} />
    </MessagePrimitive.Root>
  );
}

```

## Standalone (no runtime)

Use either player directly when the URL is already part of your app state.

```tsx
import {
  AudioPlayer,
  VideoPlayer,
} from "@/components/assistant-ui/elements/media-player";

<AudioPlayer
  src="https://cdn.example.com/briefing.mp3"
  title="Daily briefing"
  durationMs={64_000}
/>

<VideoPlayer
  src="https://cdn.example.com/overview.mp4"
  title="Overview"
  ratio="16:9"
/>

```
