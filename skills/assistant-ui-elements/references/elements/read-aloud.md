# Read aloud  (`read-aloud`)

An answer played back, the spoken word lit as it goes, speed under your thumb.

- 完整文件：`references/pages/read-aloud.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-read-aloud.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-read-aloud.md`

## With a runtime

Text-to-speech in assistant-ui is a speech adapter on the runtime plus a coarse `status` on the message being read. The runtime state has no word boundary or elapsed-time field, so the highlighted word, the elapsed and total time, and the rate come from your adapter or your app. This part of the runtime is experimental and may change.

```tsx
"use client";

import { WebSpeechSynthesisAdapter } from "@assistant-ui/react";
import { useChatRuntime } from "@assistant-ui/ai-sdk";

const runtime = useChatRuntime({
  adapters: { speech: new WebSpeechSynthesisAdapter() },
});

```

## Standalone (no runtime)

Standalone, `ReadAloud` is a pure readout: give it the words, which index is being spoken, and the transport state, and it renders the highlight and the progress bar.

```tsx
"use client";

import { useState } from "react";
import { ReadAloud } from "@/components/assistant-ui/elements/read-aloud";

const words = "Paris is the capital of France.".split(" ");

export function Playback() {
  const [spokenIndex, setSpokenIndex] = useState(0);
  const [playing, setPlaying] = useState(false);
  const [rate, setRate] = useState(1);

  return (
    <ReadAloud
      words={words}
      spokenIndex={spokenIndex}
      playing={playing}
      rate={rate}
      elapsed="0:02"
      duration="0:05"
      onToggle={() => setPlaying((p) => !p)}
      onRateChange={() => setRate((r) => (r >= 2 ? 1 : r + 0.25))}
    />
  );
}

```
