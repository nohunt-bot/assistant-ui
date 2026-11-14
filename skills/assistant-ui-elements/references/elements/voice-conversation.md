# Voice conversation  (`voice-conversation`)

A live call: the orb tracks your voice, the caption names the turn, the transcript follows.

- 完整文件：`references/pages/voice-conversation.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/voice-conversation.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-voice-conversation.md`

## With a runtime

assistant-ui models a realtime voice call as `s.thread.voice`, populated once you call `connectVoice()` against a runtime configured with a realtime voice adapter. [`/elements/orb`](/elements/orb) covers wiring that adapter and its own states in full; this page covers the surrounding call screen.

```tsx
"use client";

import { AuiIf } from "@assistant-ui/react";
import { VoiceConversation } from "@/components/assistant-ui/elements/voice-conversation.aui";

function LiveCallScreen() {
  return (
    <AuiIf condition={(s) => s.thread.capabilities.voice}>
      <VoiceConversation />
    </AuiIf>
  );
}

```

## Standalone (no runtime)

Standalone, every part of the call is a prop: which mode it's in, how loud the input is, and the turns said so far.

```tsx
"use client";

import { useState } from "react";
import { VoiceConversation, type VoiceMode, type VoiceTurn } from "@/components/assistant-ui/elements/voice-conversation";

export function Call() {
  const [mode, setMode] = useState<VoiceMode>("connecting");
  const [muted, setMuted] = useState(false);
  const [transcript, setTranscript] = useState<VoiceTurn[]>([]);

  return (
    <VoiceConversation
      mode={mode}
      amplitude={mode === "listening" ? 0.6 : 0}
      transcript={transcript}
      muted={muted}
      onToggleMute={() => setMuted((m) => !m)}
      onInterrupt={mode === "speaking" ? () => setMode("listening") : undefined}
      onEnd={() => setMode("connecting")}
    />
  );
}

```
