# Elements Attachment State（`aui/elements-attachment-state`，radix）

Shared attachment status and preview state.

- 直接依賴：`aui/use-attachment-src`
- 完整安裝順序（依賴在前，含本項）：`ui/utils`, `aui/use-attachment-src`, `aui/elements-attachment-state`
- npm（整組）：`npm install cn @assistant-ui/react@^0.15.36 zustand`

## 檔案：`components/assistant-ui/elements/attachment-state.ts`

```ts
"use client";

import { useAui, useAuiState } from "@assistant-ui/react";
import { useAttachmentSrc } from "@/hooks/use-attachment-src";

export function useAttachmentState() {
  const aui = useAui();
  const isComposer = aui.attachment.source !== "message";
  const src = useAttachmentSrc();

  const isImage = useAuiState((s) => s.attachment.type === "image");
  const name = useAuiState((s) => s.attachment.name);
  const typeLabel = useAuiState((s) => {
    const type = s.attachment.type;
    switch (type) {
      case "image":
        return "Image";
      case "document":
        return "Document";
      case "file":
        return "File";
      default:
        return type;
    }
  });

  // An attachment on a submission is still being prepared, whether or not the
  // adapter reports progress while it uploads.
  const uploadState = useAuiState((s) =>
    s.attachment.status.type === "incomplete" &&
    s.attachment.status.reason === "error"
      ? "error"
      : s.attachment.status.type === "running" ||
          (s.optional.message?.submission !== undefined &&
            s.attachment.status.type !== "complete")
        ? "uploading"
        : undefined,
  );
  const isUploading = uploadState === "uploading";
  const isError = uploadState === "error";

  const errorMessage = useAuiState((s) =>
    s.attachment.status.type === "incomplete" &&
    s.attachment.status.reason === "error"
      ? (s.attachment.status.message ?? "Upload failed")
      : undefined,
  );

  return {
    isComposer,
    src,
    isImage,
    name,
    typeLabel,
    isUploading,
    isError,
    errorMessage,
  };
}
```
