# Orb  (`orb`)

The realtime voice orb, with connection, mute, and speaking state controls.

- 完整文件：`references/pages/orb.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/thread.md`、`source/<flavor>/aui/voice.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-voice.md`

## With a runtime

A voice session needs a `RealtimeVoiceAdapter` on the runtime before any of this renders. See the [Realtime Voice guide](/docs/guides/voice) for adapter setup.

```tsx
import { useChatRuntime } from "@assistant-ui/ai-sdk";

const runtime = useChatRuntime({
  adapters: {
    voice: myVoiceAdapter,
  },
});

```

## Standalone (no runtime)

Standalone, `VoiceOrb` is a controlled visual: you own the state and the volume, and it owns the WebGL animation.

```tsx
"use client";

import { useState } from "react";
import {
  VoiceOrb,
  type VoiceOrbState,
} from "@/components/assistant-ui/elements/voice";

export function Call() {
  const [state, setState] = useState<VoiceOrbState>("idle");
  return <VoiceOrb state={state} volume={0} />;
}

```
