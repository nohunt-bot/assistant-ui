# Code runner  (`code-runner`)

A snippet with a run button, and the output it produced attached below it.

- 完整文件：`references/pages/code-runner.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-code-runner.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-code-runner.md`

## With a runtime

A tool the user completes by pressing a button, rather than one that executes the moment the model calls it, is a `"human"` toolkit entry: it has no `execute`, so the call stays open until the renderer calls `addResult`.

```tsx
"use client";

import { useState } from "react";
import { defineToolkit, type ToolCallMessagePartComponent } from "@assistant-ui/react";
import { CodeRunner, type RunState } from "@/components/assistant-ui/elements/code-runner";
import { z } from "zod";

type RunArgs = { language: string; code: string };
type RunResult = { output: string[]; durationMs: number };

const RunCodeUI: ToolCallMessagePartComponent<RunArgs, RunResult> = ({ args, result, addResult }) => {
  const [state, setState] = useState<RunState>("idle");
  const [output, setOutput] = useState<string[]>([]);

  return (
    <CodeRunner
      language={args.language}
      code={args.code}
      state={result ? "ok" : state}
      output={result?.output ?? output}
      durationMs={result?.durationMs}
      onRun={async () => {
        setState("running");
        const startedAt = Date.now();
        const lines = await runInSandbox(args.code);
        setOutput(lines);
        addResult({ output: lines, durationMs: Date.now() - startedAt });
      }}
    />
  );
};

const toolkit = defineToolkit({
  run_code: {
    type: "human",
    description: "Run a code snippet after the user presses run.",
    parameters: z.object({ language: z.string(), code: z.string() }),
    render: RunCodeUI,
  },
});

```

## Standalone (no runtime)

Standalone, the element is a controlled component: you own the run state and push output into it as the run produces it.

```tsx
"use client";

import { useState } from "react";
import { CodeRunner, type RunState } from "@/components/assistant-ui/elements/code-runner";

const code = `console.log("hello")`;

export function Snippet() {
  const [state, setState] = useState<RunState>("idle");
  const [output, setOutput] = useState<string[]>([]);
  return <CodeRunner language="js" code={code} state={state} output={output} onRun={run} />;
}

```
