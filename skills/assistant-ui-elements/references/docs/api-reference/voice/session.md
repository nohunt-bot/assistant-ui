# Voice Sessions
URL: /docs/api-reference/voice/session

Create and control realtime assistant-ui voice sessions, state, controls, and helpers.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### createVoiceSession

- `options`: `{ abortSignal?: AbortSignal; }`

  - `abortSignal?`: `AbortSignal`

    - `aborted`: `boolean`
    - `onabort`: `((this:AbortSignal,ev:Event)=>any)|null`
    - `reason`: `any`
    - `throwIfAborted`: `() => void`
    - `addEventListener`: `{ <K>(type: K, listener: (this: AbortSignal, ev: AbortSignalEventMap[K]) => any, options?: boolean | AddEventListenerOptions): void; (type: string, listener: EventListenerOrEventListenerObject, options?: boolean | AddEventListenerOptions): void; }`
    - `removeEventListener`: `{ <K>(type: K, listener: (this: AbortSignal, ev: AbortSignalEventMap[K]) => any, options?: boolean | EventListenerOptions): void; (type: string, listener: EventListenerOrEventListenerObject, options?: boolean | EventListenerOptions): void; }`
    - `dispatchEvent`: `(event: Event) => boolean` — The \*\*\`dispatchEvent()\`\*\* method of the EventTarget sends an Event to the object, (synchronously) invoking the affected event listeners in the appropriate order. The normal event processing rules (including the capturing and optional bubbling phase) also apply to events dispatched manually with dispatchEvent(). [MDN Reference](https://developer.mozilla.org/docs/Web/API/EventTarget/dispatchEvent)

- `setup`: `(helpers: VoiceSessionHelpers) => Promise<VoiceSessionControls>`

### GeminiLiveVoiceAdapter

Connects browser microphone audio directly to the Gemini Live API over a WebSocket. Configure the model, voice, and transcription on the server through the ephemeral token's connection constraints. Each connection starts a new conversation; existing thread history and assistant-ui tools are not sent to the voice model, and sessions end when Google's session limit is reached.

- `constructor?`: `(options: GeminiLiveVoiceAdapterOptions) => GeminiLiveVoiceAdapter`
- `connect?`: `(options: { abortSignal?: AbortSignal; }) => RealtimeVoiceAdapter.Session`

### OpenAIRealtimeVoiceAdapter

Connects browser microphone audio directly to OpenAI Realtime over WebRTC. Configure audio output, input transcription, and automatic turn detection on the server. Each connection starts a new conversation; existing thread history and assistant-ui tools are not sent to the voice model.

- `constructor?`: `(options: OpenAIRealtimeVoiceAdapterOptions) => OpenAIRealtimeVoiceAdapter`
- `connect?`: `(options: { abortSignal?: AbortSignal; }) => RealtimeVoiceAdapter.Session`

### RealtimeVoiceAdapter

- `connect`: `(options: { abortSignal?: AbortSignal; }) => RealtimeVoiceAdapter.Session`

### useVoiceControls

```
const useVoiceControls: () => { connect: () => void; disconnect: () => void; mute: () => void; unmute: () => void; };
```

### useVoiceState

```
const useVoiceState: () => VoiceSessionState | undefined;
```

### useVoiceVolume

```
const useVoiceVolume: () => number;
```

### VoiceSessionControls

- `disconnect`: `() => void`
- `mute`: `() => void`
- `unmute`: `() => void`
- `sendText?`: `((text: string) => void | Promise<void>) | undefined` — Delivers typed text to the provider with the same acceptance ordering as \`RealtimeVoiceAdapter.Session.sendText\`: deliver any remaining updates for an interrupted reply, resolve when the new turn is accepted, then forward its reply transcripts in later events. The session exposes \`sendText\` once these controls resolve and repeats its running status when they land after it, so status listeners can re-read the session; the runtime records the typed turn itself.

### VoiceSessionHelpers

- `setStatus`: `(status: RealtimeVoiceAdapter.Status) => void`
- `end`: `(reason: "finished" | "cancelled" | "error", error?: unknown) => void`
- `emitTranscript`: `(item: RealtimeVoiceAdapter.TranscriptItem) => void`
- `emitMode`: `(mode: RealtimeVoiceAdapter.Mode) => void`
- `emitVolume`: `(volume: number) => void`
- `isDisposed`: `() => boolean`

### VoiceSessionState

- `status`: `RealtimeVoiceAdapter.Status`
  - `type`: `"starting" | "running"`
- `isMuted`: `boolean`
- `mode`: `RealtimeVoiceAdapter.Mode`
- `canSendText`: `boolean` — Whether the running session takes typed text. While true, \`append\` routes a plain text user message into the session and the thread composer can send.