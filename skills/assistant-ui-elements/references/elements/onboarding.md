# Onboarding  (`onboarding`)

First run: three moves that teach what this assistant is actually for.

- 完整文件：`references/pages/onboarding.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-onboarding.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-onboarding.md`

## With a runtime

The steps, their copy, and their order are content you write, so there is nothing to read from `useAuiState`. The runtime is involved only when the tour ends: "Skip" and the last step's "Start" hand off to the thread.

```tsx
"use client";

import { useState } from "react";
import { useAui } from "@assistant-ui/react";
import {
  Onboarding,
  type OnboardingStep,
} from "@/components/assistant-ui/elements/onboarding";

const STEPS: OnboardingStep[] = [
  { title: "Ask anything", body: "Type a question, it answers in place.", example: "Summarize this PDF" },
  { title: "It can act, not just answer", body: "Grant a tool and it can search, run code, or call your API.", example: "Check the weather in Tokyo" },
  { title: "Every answer is a branch", body: "Regenerating never loses the original; step between versions.", example: "n / m stepper on any reply" },
];

export function FirstRun({ onFinish }: { onFinish: () => void }) {
  const aui = useAui();
  const [index, setIndex] = useState(0);
  const last = index >= STEPS.length - 1;

  const finish = () => {
    aui.threads.switchToNewThread();
    onFinish();
  };

  return (
    <Onboarding
      steps={STEPS}
      index={index}
      onNext={() => (last ? finish() : setIndex((i) => i + 1))}
      onSkip={finish}
    />
  );
}

```

## Standalone (no runtime)

Standalone, the tour is entirely local: `index` is state you hold, and `onNext` deciding whether to advance or finish is the only logic involved.

```tsx
"use client";

import { useState } from "react";
import {
  Onboarding,
  type OnboardingStep,
} from "@/components/assistant-ui/elements/onboarding";

const steps: OnboardingStep[] = [
  { title: "Welcome", body: "Three quick things before you start.", example: "…" },
  { title: "Ask in plain language", body: "No special syntax needed.", example: "What changed in v4?" },
];

export function Tour({ onDone }: { onDone: () => void }) {
  const [index, setIndex] = useState(0);
  const last = index >= steps.length - 1;

  return (
    <Onboarding
      steps={steps}
      index={index}
      onNext={() => (last ? onDone() : setIndex(index + 1))}
      onSkip={onDone}
    />
  );
}

```
