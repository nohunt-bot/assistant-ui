# Voice conversation（`aui/voice-conversation`，base）

The live call screen bound to the thread's voice session: orb, caption, transcript, mute, and end call.

- 直接依賴：`aui/elements-voice-conversation`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/elements-surfaces`, `aui/elements-range`, `aui/elements-voice-conversation`, `aui/voice-conversation`
- npm（整組）：`npm install cn tw-shimmer@^0.4.14 lucide-react @assistant-ui/react@^0.15.36`

## 檔案：`components/assistant-ui/elements/voice-conversation.aui.tsx`

```tsx
"use client";

import {
  type TextMessagePart,
  type ThreadMessage,
  useAuiState,
  useVoiceControls,
  useVoiceState,
  useVoiceVolume,
  type VoiceSessionState,
} from "@assistant-ui/react";
import { type FC, useMemo, useState } from "react";
import {
  VoiceConversation as VoiceConversationBase,
  type VoiceMode,
  type VoiceTurn,
} from "./voice-conversation";

export type { VoiceMode, VoiceTurn } from "./voice-conversation";

const deriveVoiceMode = (
  voice: VoiceSessionState | undefined,
): VoiceMode | undefined => {
  if (voice === undefined || voice.status.type === "ended") return undefined;
  if (voice.status.type === "starting") return "connecting";
  return voice.mode;
};

const toVoiceTurn = (message: ThreadMessage): VoiceTurn => ({
  id: message.id,
  role: message.role === "user" ? "user" : "assistant",
  text: message.content
    .filter((part): part is TextMessagePart => part.type === "text")
    .map((part) => part.text)
    .join(""),
});

export const useVoiceTranscript = (): readonly VoiceTurn[] => {
  const active = deriveVoiceMode(useVoiceState()) !== undefined;
  const messages = useAuiState((s) => s.thread.messages);
  const [session, setSession] = useState({ active, start: messages.length });
  if (session.active !== active) {
    setSession({ active, start: messages.length });
  }
  const start = session.active === active ? session.start : messages.length;

  return useMemo(
    () =>
      messages
        .slice(start)
        .filter((message) => message.metadata.modality === "voice")
        .map(toVoiceTurn),
    [messages, start],
  );
};

export const VoiceConversation: FC<{ className?: string }> = ({
  className,
}) => {
  const voice = useVoiceState();
  const amplitude = useVoiceVolume();
  const transcript = useVoiceTranscript();
  const { mute, unmute, disconnect } = useVoiceControls();
  const mode = deriveVoiceMode(voice);
  if (mode === undefined || voice === undefined) return null;

  return (
    <VoiceConversationBase
      className={className}
      mode={mode}
      amplitude={amplitude}
      transcript={transcript.slice(-2)}
      muted={voice.isMuted}
      onToggleMute={voice.isMuted ? unmute : mute}
      onEnd={disconnect}
    />
  );
};
```
