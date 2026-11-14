# Voice（`aui/voice`，radix）

Realtime voice session controls with connect, mute, and a status indicator.

- 直接依賴：`aui/elements-voice`, `ui/button`, `aui/tooltip-icon-button`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/elements-voice`, `ui/button`, `ui/tooltip`, `aui/tooltip-icon-button`, `aui/voice`
- npm（整組）：`npm install cn radix-ui @assistant-ui/react@^0.15.36 lucide-react`

## 檔案：`components/assistant-ui/elements/voice.aui.tsx`

```tsx
"use client";

import { TooltipIconButton } from "@/components/assistant-ui/elements/tooltip-icon-button";
import { Button } from "@/components/ui/button";
import { cn } from "@/lib/utils";
import {
  AuiIf,
  useAuiState,
  useVoiceControls,
  useVoiceState,
  useVoiceVolume,
} from "@assistant-ui/react";
import { MicIcon, MicOffIcon, PhoneIcon, PhoneOffIcon } from "lucide-react";
import { type FC, memo } from "react";
import {
  VoiceOrb as VoiceOrbBase,
  type VoiceOrbState,
  type VoiceOrbVariant,
} from "./voice";

export type { VoiceOrbState, VoiceOrbVariant } from "./voice";

export type VoiceOrbProps = {
  state?: VoiceOrbState;
  variant?: VoiceOrbVariant;
  className?: string;
};

export function deriveVoiceOrbState(
  voiceState: ReturnType<typeof useVoiceState>,
): VoiceOrbState {
  if (!voiceState) return "idle";
  if (voiceState.status.type === "starting") return "connecting";
  if (voiceState.status.type === "ended") return "idle";
  if (voiceState.isMuted) return "muted";
  if (voiceState.mode === "speaking") return "speaking";
  return "listening";
}
export const VoiceOrb: FC<VoiceOrbProps> = memo(
  ({ state: stateProp, ...rest }) => {
    const voiceState = useVoiceState();
    const volume = useVoiceVolume();
    const state = stateProp ?? deriveVoiceOrbState(voiceState);
    return <VoiceOrbBase state={state} volume={volume} {...rest} />;
  },
);

VoiceOrb.displayName = "VoiceOrb";

export const VoiceControl: FC<{ className?: string }> = ({ className }) => {
  return (
    <div
      className={cn(
        "aui-voice-control flex items-center gap-2 border-b px-4 py-2",
        className,
      )}
    >
      <VoiceStatusDot />

      <AuiIf
        condition={(s) =>
          s.thread.voice == null || s.thread.voice.status.type === "ended"
        }
      >
        <VoiceConnectButton />
      </AuiIf>

      <AuiIf condition={(s) => s.thread.voice?.status.type === "starting"}>
        <span className="aui-voice-status text-muted-foreground text-sm">
          Connecting...
        </span>
      </AuiIf>

      <AuiIf condition={(s) => s.thread.voice?.status.type === "running"}>
        <VoiceMuteButton />
        <VoiceDisconnectButton />
      </AuiIf>
    </div>
  );
};

export const VoiceStatusDot: FC = () => {
  const voiceState = useVoiceState();
  const state = deriveVoiceOrbState(voiceState);

  return (
    <span
      className={cn(
        "aui-voice-status-dot size-2.5 shrink-0 rounded-full transition-all duration-300",
        state === "idle" && "bg-muted-foreground",
        state === "connecting" && "animate-pulse bg-amber-500",
        state === "listening" && "bg-green-500",
        state === "speaking" && "bg-green-500",
        state === "muted" && "bg-destructive",
      )}
    />
  );
};

export const VoiceConnectButton: FC = () => {
  const { connect } = useVoiceControls();
  const runOwnsThread = useAuiState(
    (s) =>
      s.thread.isRunning ||
      s.thread.messages.at(-1)?.status?.type === "requires-action",
  );
  return (
    <Button
      variant="default"
      size="sm"
      className="aui-voice-connect gap-1.5 rounded-lg"
      disabled={runOwnsThread}
      onClick={() => connect()}
    >
      <PhoneIcon className="size-4" />
      Connect
    </Button>
  );
};

export const VoiceMuteButton: FC = () => {
  const voiceState = useVoiceState();
  const { mute, unmute } = useVoiceControls();
  const isMuted = voiceState?.isMuted ?? false;

  return (
    <TooltipIconButton
      tooltip={isMuted ? "Unmute" : "Mute"}
      className="aui-voice-mute"
      onClick={() => (isMuted ? unmute() : mute())}
    >
      {isMuted ? <MicOffIcon /> : <MicIcon />}
    </TooltipIconButton>
  );
};

export const VoiceDisconnectButton: FC = () => {
  const { disconnect } = useVoiceControls();
  return (
    <TooltipIconButton
      tooltip="Disconnect"
      className="aui-voice-disconnect text-destructive hover:text-destructive"
      onClick={() => disconnect()}
    >
      <PhoneOffIcon />
    </TooltipIconButton>
  );
};
```
