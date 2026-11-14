# Dictation  (`composer-voice`)

The mic morphs the input into a live waveform, then lands the transcript as text.

- 完整文件：`references/pages/composer-voice.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：沒有同名的原始碼檔，照完整文件 Getting started 的 import 路徑找 `source/<flavor>/aui/` 裡對應的檔
- 安裝（standalone）：`source/<flavor>/aui/elements-composer.md`

## With a runtime

Dictation is opt-in, the same way attachments are: without a `DictationAdapter` configured, `s.thread.capabilities.dictation` is `false` and `ComposerPrimitive.Dictate` renders disabled.

```tsx
import { useLocalRuntime, WebSpeechDictationAdapter } from "@assistant-ui/react";

const runtime = useLocalRuntime(chatModel, {
  adapters: {
    dictation: WebSpeechDictationAdapter.isSupported()
      ? new WebSpeechDictationAdapter({ interimResults: true })
      : undefined,
  },
});

```

## Standalone (no runtime)

Standalone, `ComposerVoice` and `ComposerVoiceButton` only render a recording state you already hold; nothing here touches the microphone. You own starting the browser's recognition API (or any other capture) and feeding `recording` and `seconds` from it.

```tsx
"use client";

import { useEffect, useState } from "react";
import { ComposerVoice, ComposerVoiceButton, ComposerInput } from "@/components/assistant-ui/elements/composer";

export function VoiceInput() {
  const [active, setActive] = useState(false); // from the first tap until the final transcript lands
  const [recording, setRecording] = useState(false);
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    if (!recording) return;
    setSeconds(0);
    const id = setInterval(() => setSeconds((s) => s + 1), 1000);
    return () => clearInterval(id);
  }, [recording]);

  const start = () => {
    setActive(true);
    setRecording(true);
    startBrowserRecognition({
      onFinal: (text) => {
        setRecording(false); // waveform settles into "Transcribing"
        commitTranscript(text).finally(() => setActive(false));
      },
    });
  };

  return active ? (
    <div className="flex items-center gap-2">
      <ComposerVoice recording={recording} seconds={seconds} className="flex-1" />
      <ComposerVoiceButton active={recording} onClick={stopBrowserRecognition} />
    </div>
  ) : (
    <div className="flex items-center gap-2">
      <ComposerInput className="flex-1" />
      <ComposerVoiceButton active={false} onClick={start} />
    </div>
  );
}

```
