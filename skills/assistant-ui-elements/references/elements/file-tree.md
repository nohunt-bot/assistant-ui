# File tree  (`file-tree`)

Everything a run touched, as a tree, with the churn spelled out per file.

- 完整文件：`references/pages/file-tree.md`（API reference、Anatomy、更多範例）
- 形式：runtime + standalone
- 安裝（runtime）：`source/<flavor>/aui/elements-file-tree.md`
- 安裝（standalone）：`source/<flavor>/aui/elements-file-tree.md`

## With a runtime

A tool result is naturally a flat list of touched files. Group it into folder headers and indented file rows before handing it to the element:

```tsx
"use client";

import type { ToolCallMessagePartComponent } from "@assistant-ui/react";
import { FileTree, type FileTreeNode } from "@/components/assistant-ui/elements/file-tree";

type ChangedFile = { path: string; additions: number; deletions: number };
type ListChangesResult = { files: readonly ChangedFile[] };

function buildFileTree(files: readonly ChangedFile[]): FileTreeNode[] {
  const nodes: FileTreeNode[] = [];
  const seenDirs = new Set<string>();

  for (const file of files) {
    const slash = file.path.lastIndexOf("/");
    const dir = slash === -1 ? "" : file.path.slice(0, slash);
    const name = slash === -1 ? file.path : file.path.slice(slash + 1);

    if (dir && !seenDirs.has(dir)) {
      seenDirs.add(dir);
      nodes.push({ path: dir, name: dir, depth: 0, kind: "folder" });
    }

    nodes.push({
      path: file.path,
      name,
      depth: dir ? 1 : 0,
      kind: "file",
      additions: file.additions,
      deletions: file.deletions,
    });
  }

  return nodes;
}

export const ListChangesToolUI: ToolCallMessagePartComponent<
  Record<string, never>,
  ListChangesResult
> = ({ result }) => {
  if (!result) return null;
  const nodes = buildFileTree(result.files);
  return (
    <FileTree
      nodes={nodes}
      visibleCount={nodes.length}
// …（截斷，完整見原始頁）
```

## Standalone (no runtime)

import { FileTree, type FileTreeNode } from "@/components/assistant-ui/elements/file-tree";

```tsx
"use client";

import { FileTree, type FileTreeNode } from "@/components/assistant-ui/elements/file-tree";

const NODES: readonly FileTreeNode[] = [
  { path: "packages/core/src", name: "packages/core/src", depth: 0, kind: "folder" },
  {
    path: "converter",
    name: "convertMessages.ts",
    depth: 1,
    kind: "file",
    additions: 24,
    deletions: 6,
  },
  { path: "changeset", name: ".changeset/tidy-pans.md", depth: 0, kind: "file", additions: 5 },
];

export function Changes() {
  return (
    <FileTree
      nodes={NODES}
      visibleCount={NODES.length}
      totalAdditions={29}
      totalDeletions={6}
    />
  );
}

```
