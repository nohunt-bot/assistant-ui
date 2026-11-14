# Utilities
URL: /docs/api-reference/utilities/miscellaneous

Miscellaneous @assistant-ui/react utilities for custom rendering, composition, and advanced assistant UI behavior.

> For AI agents: a documentation index is available at [llms.txt](/llms.txt). Use `.md` for canonical markdown pages; `.mdx` is kept as a backwards-compatible alias on supported URL paths.

## API Reference

### AssistantCloud

- `constructor?`: `(config: AssistantCloudConfig) => AssistantCloud`
- `threads?`: `AssistantCloudThreads`
- `projects?`: `AssistantCloudProjects`
- `auth?`: `{ tokens: AssistantCloudAuthTokens; invalidate: () => void; }`
- `runs?`: `AssistantCloudRuns`
- `files?`: `AssistantCloudFiles`
- `events?`: `AssistantCloudEvents`
- `scores?`: `AssistantCloudScores`
- `telemetry?`: `AssistantCloudTelemetryConfig`
- `registerSdk?`: `(sdk: SdkIdentity) => void`

### AuiConfig

Builds a config for [AuiProvider](/docs/api-reference/context-providers/assistant-runtime-provider#auiprovider); the `config` prop only accepts configs built with this helper.

A config is plain data: it can be hoisted to module scope, created inline per render, or memoized — the provider never relies on config identity.

```
const aui = useAui();
const config = AuiConfig({
  message: Derived({
    source: "thread",
    query: { index: 0 },
    get: (aui) => aui.thread.message({ index: 0 }),
  }),
});

<AuiProvider extends={aui} config={config}>{children}</AuiProvider>;
```

- `thread?`: `ClientElement<"thread"> | DerivedElement<"thread">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `message?`: `ClientElement<"message"> | DerivedElement<"message">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `threads?`: `ClientElement<"threads"> | DerivedElement<"threads">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `threadListItem?`: `ClientElement<"threadListItem"> | DerivedElement<"threadListItem">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `part?`: `ClientElement<"part"> | DerivedElement<"part">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `composer?`: `ClientElement<"composer"> | DerivedElement<"composer">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `attachment?`: `ClientElement<"attachment"> | DerivedElement<"attachment">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `modelContext?`: `ClientElement<"modelContext"> | DerivedElement<"modelContext">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `suggestions?`: `ClientElement<"suggestions"> | DerivedElement<"suggestions">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `suggestion?`: `ClientElement<"suggestion"> | DerivedElement<"suggestion">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `chainOfThought?`: `ClientElement<"chainOfThought"> | DerivedElement<"chainOfThought">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `queueItem?`: `ClientElement<"queueItem"> | DerivedElement<"queueItem">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `task?`: `ClientElement<"task"> | DerivedElement<"task">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `tools?`: `ClientElement<"tools"> | DerivedElement<"tools">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `dataRenderers?`: `ClientElement<"dataRenderers"> | DerivedElement<"dataRenderers">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `interactables?`: `ClientElement<"interactables"> | DerivedElement<"interactables">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

- `unstable_interactables?`: `ClientElement<"unstable_interactables"> | DerivedElement<"unstable_interactables">`

  - `hook`: `(...args: any[]) => V`
  - `args`: `readonly unknown[]`
  - `key?`: `string | number`
  - `deps?`: `readonly unknown[]`

### ChainOfThoughtClient

- `0`: `ChainOfThoughtClient props["0"]`

  - `parts`: `readonly ChainOfThoughtPart[]`
  - `getMessagePart`: `(selector: { index: number }) => PartMethods`

- `length`: `1`

- `toString`: `() => string`

- `toLocaleString`: `{ (): string; (locales: string | string[], options?: Intl.NumberFormatOptions & Intl.DateTimeFormatOptions): string; }`

- `pop`: `() => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }`

- `push`: `(...items: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => number`

- `concat`: `{ (...items: ConcatArray<{ parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }>[]): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]; (...items: ({ parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; } | ConcatArray<{ parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }>)[]): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]; }`

- `join`: `(separator?: string) => string`

- `reverse`: `() => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]`

- `shift`: `() => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }`

- `slice`: `(start?: number, end?: number) => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]`

- `sort`: `(compareFn?: ((a: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, b: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }) => number) | undefined) => [{ parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }]`

- `splice`: `{ (start: number, deleteCount?: number): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]; (start: number, deleteCount: number, ...items: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]; }`

- `unshift`: `(...items: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => number`

- `indexOf`: `(searchElement: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, fromIndex?: number) => number`

- `lastIndexOf`: `(searchElement: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, fromIndex?: number) => number`

- `every`: `{ <S>(predicate: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => value is S, thisArg?: any): this is S[]; (predicate: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => unknown, thisArg?: any): boolean; }`

- `some`: `(predicate: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => unknown, thisArg?: any) => boolean`

- `forEach`: `(callbackfn: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => void, thisArg?: any) => void`

- `map`: `<U>(callbackfn: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => U, thisArg?: any) => U[]`

- `filter`: `{ <S>(predicate: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => value is S, thisArg?: any): S[]; (predicate: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => unknown, thisArg?: any): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]; }`

- `reduce`: `{ (callbackfn: (previousValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, currentValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, currentIndex: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }; (callbackfn: (previousValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, currentValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, currentIndex: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, initialValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }; <U>(callbackfn: (previousValue: U, currentValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, currentIndex: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => U, initialValue: U): U; }`

- `reduceRight`: `{ (callbackfn: (previousValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, currentValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, currentIndex: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }; (callbackfn: (previousValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, currentValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, currentIndex: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, initialValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }; <U>(callbackfn: (previousValue: U, currentValue: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, currentIndex: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => U, initialValue: U): U; }`

- `find`: `{ <S>(predicate: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, obj: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, obj: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => unknown, thisArg?: any): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; } | undefined; }`

- `findIndex`: `(predicate: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, obj: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => unknown, thisArg?: any) => number`

- `fill`: `(value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, start?: number, end?: number) => [{ parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }]`

- `copyWithin`: `(target: number, start: number, end?: number) => [{ parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }]`

- `entries`: `() => ArrayIterator<[number, { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }]>`

- `keys`: `() => ArrayIterator<number>`

- `values`: `() => ArrayIterator<{ parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }>`

- `includes`: `(searchElement: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, fromIndex?: number) => boolean`

- `flatMap`: `<U, This>(callback: (this: This, value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => U | readonly U[], thisArg?: This | undefined) => U[]`

- `flat`: `<A, D>(this: A, depth?: D | undefined) => FlatArray<A, D>[]`

- `at`: `(index: number) => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }`

- `findLast`: `{ <S>(predicate: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => unknown, thisArg?: any): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; } | undefined; }`

- `findLastIndex`: `(predicate: (value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, index: number, array: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]) => unknown, thisArg?: any) => number`

- `toReversed`: `() => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]`

- `toSorted`: `(compareFn?: ((a: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }, b: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }) => number) | undefined) => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]`

- `toSpliced`: `{ (start: number, deleteCount: number, ...items: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]; (start: number, deleteCount?: number): { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]; }`

- `with`: `(index: number, value: { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }) => { parts: readonly ChainOfThoughtPart[]; getMessagePart: (selector: { index: number; }) => PartMethods; }[]`

### CloudRendererHost

- `props`: `CloudRendererHostProps`

  - `children?`: `ReactNode` — The app's thread UI, usually its own \`\<Thread />\`.

  - `allowedOrigins?`: `readonly string[] | undefined` — Dashboard origins allowed to send a conversation. Defaults to Assistant Cloud's dashboard.

  - `aui?`: `AssistantRuntimeProvider.Props["aui"]` — The same client or config the app passes to AssistantRuntimeProvider, so tool UIs, data UIs and generative UI render as they do in the app.

    - `thread`: `AssistantClientAccessor<"thread">`

    - `message`: `AssistantClientAccessor<"message">`

    - `threads`: `AssistantClientAccessor<"threads">`

    - `threadListItem`: `AssistantClientAccessor<"threadListItem">`

    - `part`: `AssistantClientAccessor<"part">`

    - `composer`: `AssistantClientAccessor<"composer">`

    - `attachment`: `AssistantClientAccessor<"attachment">`

    - `modelContext`: `AssistantClientAccessor<"modelContext">`

    - `suggestions`: `AssistantClientAccessor<"suggestions">`

    - `suggestion`: `AssistantClientAccessor<"suggestion">`

    - `chainOfThought`: `AssistantClientAccessor<"chainOfThought">`

    - `queueItem`: `AssistantClientAccessor<"queueItem">`

    - `task`: `AssistantClientAccessor<"task">`

    - `tools`: `AssistantClientAccessor<"tools">`

    - `dataRenderers`: `AssistantClientAccessor<"dataRenderers">`

    - `interactables`: `AssistantClientAccessor<"interactables">`

    - `unstable_interactables`: `AssistantClientAccessor<"unstable_interactables">`

    - `optional`: `CloudRendererHostProps["aui"]["optional"]`

      - `thread?`: `AssistantClientAccessor<"thread">`
      - `message?`: `AssistantClientAccessor<"message">`
      - `threads?`: `AssistantClientAccessor<"threads">`
      - `threadListItem?`: `AssistantClientAccessor<"threadListItem">`
      - `part?`: `AssistantClientAccessor<"part">`
      - `composer?`: `AssistantClientAccessor<"composer">`
      - `attachment?`: `AssistantClientAccessor<"attachment">`
      - `modelContext?`: `AssistantClientAccessor<"modelContext">`
      - `suggestions?`: `AssistantClientAccessor<"suggestions">`
      - `suggestion?`: `AssistantClientAccessor<"suggestion">`
      - `chainOfThought?`: `AssistantClientAccessor<"chainOfThought">`
      - `queueItem?`: `AssistantClientAccessor<"queueItem">`
      - `task?`: `AssistantClientAccessor<"task">`
      - `tools?`: `AssistantClientAccessor<"tools">`
      - `dataRenderers?`: `AssistantClientAccessor<"dataRenderers">`
      - `interactables?`: `AssistantClientAccessor<"interactables">`
      - `unstable_interactables?`: `AssistantClientAccessor<"unstable_interactables">`

    - `subscribe`: `(listener: () => void) => Unsubscribe`

    - `on`: `<TEvent extends AssistantEventName>(selector: AssistantEventSelector<TEvent>, callback: AssistantEventCallback<TEvent>) => Unsubscribe`

  - `config?`: `AssistantRuntimeProvider.Props["config"]`

    - `thread?`: `ClientElement<"thread"> | DerivedElement<"thread">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `message?`: `ClientElement<"message"> | DerivedElement<"message">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `threads?`: `ClientElement<"threads"> | DerivedElement<"threads">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `threadListItem?`: `ClientElement<"threadListItem"> | DerivedElement<"threadListItem">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `part?`: `ClientElement<"part"> | DerivedElement<"part">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `composer?`: `ClientElement<"composer"> | DerivedElement<"composer">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `attachment?`: `ClientElement<"attachment"> | DerivedElement<"attachment">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `modelContext?`: `ClientElement<"modelContext"> | DerivedElement<"modelContext">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `suggestions?`: `ClientElement<"suggestions"> | DerivedElement<"suggestions">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `suggestion?`: `ClientElement<"suggestion"> | DerivedElement<"suggestion">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `chainOfThought?`: `ClientElement<"chainOfThought"> | DerivedElement<"chainOfThought">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `queueItem?`: `ClientElement<"queueItem"> | DerivedElement<"queueItem">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `task?`: `ClientElement<"task"> | DerivedElement<"task">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `tools?`: `ClientElement<"tools"> | DerivedElement<"tools">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `dataRenderers?`: `ClientElement<"dataRenderers"> | DerivedElement<"dataRenderers">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `interactables?`: `ClientElement<"interactables"> | DerivedElement<"interactables">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

    - `unstable_interactables?`: `ClientElement<"unstable_interactables"> | DerivedElement<"unstable_interactables">`

      - `hook`: `(...args: any[]) => V`
      - `args`: `readonly unknown[]`
      - `key?`: `string | number`
      - `deps?`: `readonly unknown[]`

### createMessageQueue

```
const createMessageQueue: (driver: MessageQueueDriver) => MessageQueueController;
```

### createSimpleTitleAdapter

```
const createSimpleTitleAdapter: () => TitleGenerationAdapter;
```

### createThreadRowsSelector

Creates a selector for `useAuiState` that flattens the current thread into rows (see ThreadRow). The selector returns the same array for the same thread state, and keeps returning the previous array and row objects while a streamed token leaves the rows unchanged, so a list keyed by `row.key` re-renders only when rows appear, disappear, regroup or a turn completes. Create one selector per list, at module scope or with `useState`.

```
const selectRows = createThreadRowsSelector({
  groupBy: groupPartByType({ reasoning: ["group-reasoning"] }),
});

function VirtualThread() {
  const rows = useAuiState(selectRows);
  // feed rows to a virtualizer and render each with ThreadPrimitive.Row
}
```

```
const createThreadRowsSelector: (options?: ThreadRowsOptions) => (state: AssistantState) => readonly ThreadRow[];
```

### defaultDirectiveFormatter

Default directive formatter using the `:type[label]{name=id}` syntax.

When `id` equals `label`, the `{name=…}` attribute is omitted for brevity. Labels containing `]` or distinct IDs containing `}` use `:type{label="..." name="..."}` with JSON-quoted values instead. The `name` field is omitted when it equals the label. Existing bracket syntax stays literal.

```
const defaultDirectiveFormatter: DirectiveFormatter;
```

### DevToolsHooks

- `static subscribe?`: `(listener: () => void) => Unsubscribe`
- `static clearEventLogs?`: `(apiId: number) => void`
- `static getApis?`: `() => Map<number, DevToolsApiEntry>`

### InMemoryThreadList

- `0`: `InMemoryThreadListProps`

  - `thread`: `(threadId: string) => ResourceElement<ClientOutput<"thread">>` — Creates the selected thread resource. The list keys the returned element by \`threadId\`, so thread-owned state does not survive a selection change.
  - `onSwitchToThread?`: `(threadId: string) => void`
  - `onSwitchToNewThread?`: `() => void`
  - `onDelete?`: `(threadId: string) => void`

- `length`: `1`

- `toString`: `() => string`

- `toLocaleString`: `{ (): string; (locales: string | string[], options?: Intl.NumberFormatOptions & Intl.DateTimeFormatOptions): string; }`

- `pop`: `() => InMemoryThreadListProps`

- `push`: `(...items: InMemoryThreadListProps[]) => number`

- `concat`: `{ (...items: ConcatArray<InMemoryThreadListProps>[]): InMemoryThreadListProps[]; (...items: (InMemoryThreadListProps | ConcatArray<InMemoryThreadListProps>)[]): InMemoryThreadListProps[]; }`

- `join`: `(separator?: string) => string`

- `reverse`: `() => InMemoryThreadListProps[]`

- `shift`: `() => InMemoryThreadListProps`

- `slice`: `(start?: number, end?: number) => InMemoryThreadListProps[]`

- `sort`: `(compareFn?: ((a: InMemoryThreadListProps, b: InMemoryThreadListProps) => number) | undefined) => [props: InMemoryThreadListProps]`

- `splice`: `{ (start: number, deleteCount?: number): InMemoryThreadListProps[]; (start: number, deleteCount: number, ...items: InMemoryThreadListProps[]): InMemoryThreadListProps[]; }`

- `unshift`: `(...items: InMemoryThreadListProps[]) => number`

- `indexOf`: `(searchElement: InMemoryThreadListProps, fromIndex?: number) => number`

- `lastIndexOf`: `(searchElement: InMemoryThreadListProps, fromIndex?: number) => number`

- `every`: `{ <S>(predicate: (value: InMemoryThreadListProps, index: number, array: InMemoryThreadListProps[]) => value is S, thisArg?: any): this is S[]; (predicate: (value: InMemoryThreadListProps, index: number, array: InMemoryThreadListProps[]) => unknown, thisArg?: any): boolean; }`

- `some`: `(predicate: (value: InMemoryThreadListProps, index: number, array: InMemoryThreadListProps[]) => unknown, thisArg?: any) => boolean`

- `forEach`: `(callbackfn: (value: InMemoryThreadListProps, index: number, array: InMemoryThreadListProps[]) => void, thisArg?: any) => void`

- `map`: `<U>(callbackfn: (value: InMemoryThreadListProps, index: number, array: InMemoryThreadListProps[]) => U, thisArg?: any) => U[]`

- `filter`: `{ <S>(predicate: (value: InMemoryThreadListProps, index: number, array: InMemoryThreadListProps[]) => value is S, thisArg?: any): S[]; (predicate: (value: InMemoryThreadListProps, index: number, array: InMemoryThreadListProps[]) => unknown, thisArg?: any): InMemoryThreadListProps[]; }`

- `reduce`: `{ (callbackfn: (previousValue: InMemoryThreadListProps, currentValue: InMemoryThreadListProps, currentIndex: number, array: InMemoryThreadListProps[]) => InMemoryThreadListProps): InMemoryThreadListProps; (callbackfn: (previousValue: InMemoryThreadListProps, currentValue: InMemoryThreadListProps, currentIndex: number, array: InMemoryThreadListProps[]) => InMemoryThreadListProps, initialValue: InMemoryThreadListProps): InMemoryThreadListProps; <U>(callbackfn: (previousValue: U, currentValue: InMemoryThreadListProps, currentIndex: number, array: InMemoryThreadListProps[]) => U, initialValue: U): U; }`

- `reduceRight`: `{ (callbackfn: (previousValue: InMemoryThreadListProps, currentValue: InMemoryThreadListProps, currentIndex: number, array: InMemoryThreadListProps[]) => InMemoryThreadListProps): InMemoryThreadListProps; (callbackfn: (previousValue: InMemoryThreadListProps, currentValue: InMemoryThreadListProps, currentIndex: number, array: InMemoryThreadListProps[]) => InMemoryThreadListProps, initialValue: InMemoryThreadListProps): InMemoryThreadListProps; <U>(callbackfn: (previousValue: U, currentValue: InMemoryThreadListProps, currentIndex: number, array: InMemoryThreadListProps[]) => U, initialValue: U): U; }`

- `find`: `{ <S>(predicate: (value: InMemoryThreadListProps, index: number, obj: InMemoryThreadListProps[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: InMemoryThreadListProps, index: number, obj: InMemoryThreadListProps[]) => unknown, thisArg?: any): InMemoryThreadListProps | undefined; }`

- `findIndex`: `(predicate: (value: InMemoryThreadListProps, index: number, obj: InMemoryThreadListProps[]) => unknown, thisArg?: any) => number`

- `fill`: `(value: InMemoryThreadListProps, start?: number, end?: number) => [props: InMemoryThreadListProps]`

- `copyWithin`: `(target: number, start: number, end?: number) => [props: InMemoryThreadListProps]`

- `entries`: `() => ArrayIterator<[number, InMemoryThreadListProps]>`

- `keys`: `() => ArrayIterator<number>`

- `values`: `() => ArrayIterator<InMemoryThreadListProps>`

- `includes`: `(searchElement: InMemoryThreadListProps, fromIndex?: number) => boolean`

- `flatMap`: `<U, This>(callback: (this: This, value: InMemoryThreadListProps, index: number, array: InMemoryThreadListProps[]) => U | readonly U[], thisArg?: This | undefined) => U[]`

- `flat`: `<A, D>(this: A, depth?: D | undefined) => FlatArray<A, D>[]`

- `at`: `(index: number) => InMemoryThreadListProps`

- `findLast`: `{ <S>(predicate: (value: InMemoryThreadListProps, index: number, array: InMemoryThreadListProps[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: InMemoryThreadListProps, index: number, array: InMemoryThreadListProps[]) => unknown, thisArg?: any): InMemoryThreadListProps | undefined; }`

- `findLastIndex`: `(predicate: (value: InMemoryThreadListProps, index: number, array: InMemoryThreadListProps[]) => unknown, thisArg?: any) => number`

- `toReversed`: `() => InMemoryThreadListProps[]`

- `toSorted`: `(compareFn?: ((a: InMemoryThreadListProps, b: InMemoryThreadListProps) => number) | undefined) => InMemoryThreadListProps[]`

- `toSpliced`: `{ (start: number, deleteCount: number, ...items: InMemoryThreadListProps[]): InMemoryThreadListProps[]; (start: number, deleteCount?: number): InMemoryThreadListProps[]; }`

- `with`: `(index: number, value: InMemoryThreadListProps) => InMemoryThreadListProps[]`

### isMessageNotSentError

```
const isMessageNotSentError: (error: unknown) => error is MessageNotSentError;
```

### MessageNotSentError

Rejection reason for a send that never reached the backend, so nothing ran and nothing is recoverable from the thread. A runtime adapter throws it from `onNew` to hand the message back to the thread composer, which restores the draft it cleared at dispatch time when nothing has claimed the composer since. Throwing it from `onEdit` reopens the unsent edit if the source message is unchanged, no newer edit has started, and the thread is still current.

- `constructor?`: `(message: string = "The message was not sent.") => MessageNotSentError`
- `[MESSAGE_NOT_SENT]?`: `true`

### readAnonymousRefreshToken

The refresh token of the anonymous identity this browser holds for `baseUrl`, or null when it has none.

```
const readAnonymousRefreshToken: (baseUrl: string) => string | null;
```

### RemoteThreadList

`AuiConfig` `threads` entry backed by a `RemoteThreadListAdapter`. Thread bodies are born from the `thread` factory inside the client tree, so any `AssistantClient` host can run a remote or cloud list. Per-thread history and attachments come from `unstable_useAdapters`. `useRemoteThreadListRuntime` uses the same hook except while it renders `unstable_Provider`, which it does only when the adapter it mounts with has one. Key the factory with `withKey` so history reloads when the visible thread changes. With `backgroundThreads`, every visited thread stays mounted: runs continue across switches, per-item `isRunning` is live, and freshly initialized threads title themselves.

- `0`: `RemoteThreadListProps`

  - `adapter`: `RemoteThreadListAdapter` — Swapping this to a different backing store does not reload the list. Call \`reload()\` after a genuine swap. A recreated object for the same store is a no-op. \`reload()\` after a different adapter instance resets selection and cached records before loading.

    - `list`: `(params?: RemoteThreadListPageOptions) => Promise<RemoteThreadListResponse>`
    - `rename`: `(remoteId: string, newTitle: string) => Promise<void>`
    - `updateCustom?`: `(remoteId: string, custom: Record<string, unknown> | undefined) => Promise<void>`
    - `archive`: `(remoteId: string) => Promise<void>`
    - `unarchive?`: `(remoteId: string) => Promise<void>` — Restores an archived thread. Omit it when the backend cannot unarchive: switching to an archived thread then opens it as archived, and an explicit unarchive rejects.
    - `delete`: `(remoteId: string) => Promise<void>`
    - `initialize`: `(threadId: string, options?: { custom?: Record<string, unknown> | undefined; }) => Promise<RemoteThreadInitializeResponse>` — Creates a thread with draft metadata passed in \`options.custom\`. Draft metadata reaches the backend only through this call, so an adapter that ignores \`options.custom\` drops it on the next \`list()\`.
    - `generateTitle`: `(remoteId: string, unstable_messages: readonly ThreadMessage[]) => Promise<AssistantStream>` — Generates a title for the thread and streams it back. Called automatically once the first message settles and, if the thread still has no title, again after its first run ends (completion, error, or cancellation). A backend that titles the thread asynchronously may keep the stream open until its title is ready. When the generation persists the title itself, the returned stream must not complete before that write has landed. Concurrent generations are ordered by stream completion, and a run whose write outlives its stream can overwrite a newer title.
    - `fetch`: `(threadId: string) => Promise<RemoteThreadMetadata>`
    - `unstable_Provider?`: `RemoteThreadListProviderComponent | undefined` (deprecated: Return the adapters from \`unstable_useAdapters\` instead. \`unstable_Provider\` will be removed in a future minor release of \`@assistant-ui/core\`.) — Optional React component wrapped around each active thread, an older face of \`unstable_useAdapters\` for injecting per-thread adapters such as history or attachments. A component that only wraps \`children\` in \`RuntimeAdapterProvider\` migrates by returning those adapters from \`unstable_useAdapters\`. \`useRemoteThreadListRuntime\` renders this component only when the adapter it mounts with has one, and that runtime then runs on the deprecated \`RemoteThreadListThreadListRuntimeCore\` instead of the \`RemoteThreadList\` client; it renders each later adapter's provider and synthesizes a \`RuntimeAdapterProvider\` from \`unstable_useAdapters\` for a later adapter without one. A hook that mounts with an adapter without this component calls \`unstable_useAdapters\` for every later adapter and ignores this component. The \`RemoteThreadList\` store entry ignores it. The Provider must render \`children\` on its first commit; deferring them behind a loading state, a Suspense boundary, or a \`useEffect\`-gated render is unsupported and leaves thread context unavailable to downstream consumers. Load data inside an always-mounted child instead.
    - `unstable_useAdapters?`: `(() => RuntimeAdapters | null | undefined) | undefined` (experimental) — Hook the \`RemoteThreadList\` store entry calls once per mounted thread body (the main-thread slot by default; every started thread with \`backgroundThreads\`), then provides to the \`thread\` factory. This is not mounted per listed thread. \`useRemoteThreadListRuntime\` also calls it except while it renders \`unstable_Provider\`. Resolve \`threadListItem\` lazily on each adapter call; do not capture it at hook mount. The hook must keep a stable hook count across adapter swaps; a different count throws. Memoize the returned object. Per-thread history also requires the \`thread\` factory to be keyed by thread id (\`withKey(id, thread(...))\`). History loaders such as \`useExternalHistory\` run once per mount; an unkeyed factory keeps one instance across switches and the next thread's messages never load.

  - `thread`: `InMemoryThreadListProps["thread"]` — Factory for the visible thread. Per-thread history reload requires a resource keyed by thread id (\`withKey(id, thread(...))\`). An unkeyed factory keeps one instance across switches, so a mount-once history loader will not fetch the next thread.

  - `threadId?`: `string | undefined`

  - `onThreadIdChange?`: `((threadId: string | undefined) => void) | undefined`

  - `onSwitchToThread?`: `((threadId: string) => void) | undefined`

  - `onSwitchToNewThread?`: `(() => void) | undefined`

  - `onDelete?`: `((threadId: string) => void) | undefined` — Called after the backing adapter successfully deletes the thread.

  - `backgroundThreads?`: `boolean | undefined` — Keeps every thread the session switched to mounted, so a run continues after the user switches away, per-item \`isRunning\` reflects background runs, and a freshly initialized thread generates its title automatically. A body unmounts on delete, detach, or an adapter replacement (\`reload()\` after swapping the adapter); visited threads otherwise stay mounted for the client's lifetime. Each body reads its own thread through the ambient \`threadListItem\` scope, and \`unstable_useAdapters\` runs once per mounted body. Off (the default), only the visible thread is mounted and a switch unmounts it. The mode is fixed at mount.

- `length`: `1`

- `toString`: `() => string`

- `toLocaleString`: `{ (): string; (locales: string | string[], options?: Intl.NumberFormatOptions & Intl.DateTimeFormatOptions): string; }`

- `pop`: `() => RemoteThreadListProps`

- `push`: `(...items: RemoteThreadListProps[]) => number`

- `concat`: `{ (...items: ConcatArray<RemoteThreadListProps>[]): RemoteThreadListProps[]; (...items: (RemoteThreadListProps | ConcatArray<RemoteThreadListProps>)[]): RemoteThreadListProps[]; }`

- `join`: `(separator?: string) => string`

- `reverse`: `() => RemoteThreadListProps[]`

- `shift`: `() => RemoteThreadListProps`

- `slice`: `(start?: number, end?: number) => RemoteThreadListProps[]`

- `sort`: `(compareFn?: ((a: RemoteThreadListProps, b: RemoteThreadListProps) => number) | undefined) => [props: RemoteThreadListProps]`

- `splice`: `{ (start: number, deleteCount?: number): RemoteThreadListProps[]; (start: number, deleteCount: number, ...items: RemoteThreadListProps[]): RemoteThreadListProps[]; }`

- `unshift`: `(...items: RemoteThreadListProps[]) => number`

- `indexOf`: `(searchElement: RemoteThreadListProps, fromIndex?: number) => number`

- `lastIndexOf`: `(searchElement: RemoteThreadListProps, fromIndex?: number) => number`

- `every`: `{ <S>(predicate: (value: RemoteThreadListProps, index: number, array: RemoteThreadListProps[]) => value is S, thisArg?: any): this is S[]; (predicate: (value: RemoteThreadListProps, index: number, array: RemoteThreadListProps[]) => unknown, thisArg?: any): boolean; }`

- `some`: `(predicate: (value: RemoteThreadListProps, index: number, array: RemoteThreadListProps[]) => unknown, thisArg?: any) => boolean`

- `forEach`: `(callbackfn: (value: RemoteThreadListProps, index: number, array: RemoteThreadListProps[]) => void, thisArg?: any) => void`

- `map`: `<U>(callbackfn: (value: RemoteThreadListProps, index: number, array: RemoteThreadListProps[]) => U, thisArg?: any) => U[]`

- `filter`: `{ <S>(predicate: (value: RemoteThreadListProps, index: number, array: RemoteThreadListProps[]) => value is S, thisArg?: any): S[]; (predicate: (value: RemoteThreadListProps, index: number, array: RemoteThreadListProps[]) => unknown, thisArg?: any): RemoteThreadListProps[]; }`

- `reduce`: `{ (callbackfn: (previousValue: RemoteThreadListProps, currentValue: RemoteThreadListProps, currentIndex: number, array: RemoteThreadListProps[]) => RemoteThreadListProps): RemoteThreadListProps; (callbackfn: (previousValue: RemoteThreadListProps, currentValue: RemoteThreadListProps, currentIndex: number, array: RemoteThreadListProps[]) => RemoteThreadListProps, initialValue: RemoteThreadListProps): RemoteThreadListProps; <U>(callbackfn: (previousValue: U, currentValue: RemoteThreadListProps, currentIndex: number, array: RemoteThreadListProps[]) => U, initialValue: U): U; }`

- `reduceRight`: `{ (callbackfn: (previousValue: RemoteThreadListProps, currentValue: RemoteThreadListProps, currentIndex: number, array: RemoteThreadListProps[]) => RemoteThreadListProps): RemoteThreadListProps; (callbackfn: (previousValue: RemoteThreadListProps, currentValue: RemoteThreadListProps, currentIndex: number, array: RemoteThreadListProps[]) => RemoteThreadListProps, initialValue: RemoteThreadListProps): RemoteThreadListProps; <U>(callbackfn: (previousValue: U, currentValue: RemoteThreadListProps, currentIndex: number, array: RemoteThreadListProps[]) => U, initialValue: U): U; }`

- `find`: `{ <S>(predicate: (value: RemoteThreadListProps, index: number, obj: RemoteThreadListProps[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: RemoteThreadListProps, index: number, obj: RemoteThreadListProps[]) => unknown, thisArg?: any): RemoteThreadListProps | undefined; }`

- `findIndex`: `(predicate: (value: RemoteThreadListProps, index: number, obj: RemoteThreadListProps[]) => unknown, thisArg?: any) => number`

- `fill`: `(value: RemoteThreadListProps, start?: number, end?: number) => [props: RemoteThreadListProps]`

- `copyWithin`: `(target: number, start: number, end?: number) => [props: RemoteThreadListProps]`

- `entries`: `() => ArrayIterator<[number, RemoteThreadListProps]>`

- `keys`: `() => ArrayIterator<number>`

- `values`: `() => ArrayIterator<RemoteThreadListProps>`

- `includes`: `(searchElement: RemoteThreadListProps, fromIndex?: number) => boolean`

- `flatMap`: `<U, This>(callback: (this: This, value: RemoteThreadListProps, index: number, array: RemoteThreadListProps[]) => U | readonly U[], thisArg?: This | undefined) => U[]`

- `flat`: `<A, D>(this: A, depth?: D | undefined) => FlatArray<A, D>[]`

- `at`: `(index: number) => RemoteThreadListProps`

- `findLast`: `{ <S>(predicate: (value: RemoteThreadListProps, index: number, array: RemoteThreadListProps[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: RemoteThreadListProps, index: number, array: RemoteThreadListProps[]) => unknown, thisArg?: any): RemoteThreadListProps | undefined; }`

- `findLastIndex`: `(predicate: (value: RemoteThreadListProps, index: number, array: RemoteThreadListProps[]) => unknown, thisArg?: any) => number`

- `toReversed`: `() => RemoteThreadListProps[]`

- `toSorted`: `(compareFn?: ((a: RemoteThreadListProps, b: RemoteThreadListProps) => number) | undefined) => RemoteThreadListProps[]`

- `toSpliced`: `{ (start: number, deleteCount: number, ...items: RemoteThreadListProps[]): RemoteThreadListProps[]; (start: number, deleteCount?: number): RemoteThreadListProps[]; }`

- `with`: `(index: number, value: RemoteThreadListProps) => RemoteThreadListProps[]`

### SingleThreadList

- `0`: `SingleThreadListProps`

  - `thread`: `ClientElement<"thread">`

    - `hook`: `(...args: any[]) => V`
    - `args`: `readonly unknown[]`
    - `key?`: `string | number`
    - `deps?`: `readonly unknown[]`

- `length`: `1`

- `toString`: `() => string`

- `toLocaleString`: `{ (): string; (locales: string | string[], options?: Intl.NumberFormatOptions & Intl.DateTimeFormatOptions): string; }`

- `pop`: `() => { thread: ClientElement<"thread">; }`

- `push`: `(...items: { thread: ClientElement<"thread">; }[]) => number`

- `concat`: `{ (...items: ConcatArray<{ thread: ClientElement<"thread">; }>[]): { thread: ClientElement<"thread">; }[]; (...items: ({ thread: ClientElement<"thread">; } | ConcatArray<{ thread: ClientElement<"thread">; }>)[]): { thread: ClientElement<"thread">; }[]; }`

- `join`: `(separator?: string) => string`

- `reverse`: `() => { thread: ClientElement<"thread">; }[]`

- `shift`: `() => { thread: ClientElement<"thread">; }`

- `slice`: `(start?: number, end?: number) => { thread: ClientElement<"thread">; }[]`

- `sort`: `(compareFn?: ((a: { thread: ClientElement<"thread">; }, b: { thread: ClientElement<"thread">; }) => number) | undefined) => [{ thread: ClientElement<"thread">; }]`

- `splice`: `{ (start: number, deleteCount?: number): { thread: ClientElement<"thread">; }[]; (start: number, deleteCount: number, ...items: { thread: ClientElement<"thread">; }[]): { thread: ClientElement<"thread">; }[]; }`

- `unshift`: `(...items: { thread: ClientElement<"thread">; }[]) => number`

- `indexOf`: `(searchElement: { thread: ClientElement<"thread">; }, fromIndex?: number) => number`

- `lastIndexOf`: `(searchElement: { thread: ClientElement<"thread">; }, fromIndex?: number) => number`

- `every`: `{ <S>(predicate: (value: { thread: ClientElement<"thread">; }, index: number, array: { thread: ClientElement<"thread">; }[]) => value is S, thisArg?: any): this is S[]; (predicate: (value: { thread: ClientElement<"thread">; }, index: number, array: { thread: ClientElement<"thread">; }[]) => unknown, thisArg?: any): boolean; }`

- `some`: `(predicate: (value: { thread: ClientElement<"thread">; }, index: number, array: { thread: ClientElement<"thread">; }[]) => unknown, thisArg?: any) => boolean`

- `forEach`: `(callbackfn: (value: { thread: ClientElement<"thread">; }, index: number, array: { thread: ClientElement<"thread">; }[]) => void, thisArg?: any) => void`

- `map`: `<U>(callbackfn: (value: { thread: ClientElement<"thread">; }, index: number, array: { thread: ClientElement<"thread">; }[]) => U, thisArg?: any) => U[]`

- `filter`: `{ <S>(predicate: (value: { thread: ClientElement<"thread">; }, index: number, array: { thread: ClientElement<"thread">; }[]) => value is S, thisArg?: any): S[]; (predicate: (value: { thread: ClientElement<"thread">; }, index: number, array: { thread: ClientElement<"thread">; }[]) => unknown, thisArg?: any): { thread: ClientElement<"thread">; }[]; }`

- `reduce`: `{ (callbackfn: (previousValue: { thread: ClientElement<"thread">; }, currentValue: { thread: ClientElement<"thread">; }, currentIndex: number, array: { thread: ClientElement<"thread">; }[]) => { thread: ClientElement<"thread">; }): { thread: ClientElement<"thread">; }; (callbackfn: (previousValue: { thread: ClientElement<"thread">; }, currentValue: { thread: ClientElement<"thread">; }, currentIndex: number, array: { thread: ClientElement<"thread">; }[]) => { thread: ClientElement<"thread">; }, initialValue: { thread: ClientElement<"thread">; }): { thread: ClientElement<"thread">; }; <U>(callbackfn: (previousValue: U, currentValue: { thread: ClientElement<"thread">; }, currentIndex: number, array: { thread: ClientElement<"thread">; }[]) => U, initialValue: U): U; }`

- `reduceRight`: `{ (callbackfn: (previousValue: { thread: ClientElement<"thread">; }, currentValue: { thread: ClientElement<"thread">; }, currentIndex: number, array: { thread: ClientElement<"thread">; }[]) => { thread: ClientElement<"thread">; }): { thread: ClientElement<"thread">; }; (callbackfn: (previousValue: { thread: ClientElement<"thread">; }, currentValue: { thread: ClientElement<"thread">; }, currentIndex: number, array: { thread: ClientElement<"thread">; }[]) => { thread: ClientElement<"thread">; }, initialValue: { thread: ClientElement<"thread">; }): { thread: ClientElement<"thread">; }; <U>(callbackfn: (previousValue: U, currentValue: { thread: ClientElement<"thread">; }, currentIndex: number, array: { thread: ClientElement<"thread">; }[]) => U, initialValue: U): U; }`

- `find`: `{ <S>(predicate: (value: { thread: ClientElement<"thread">; }, index: number, obj: { thread: ClientElement<"thread">; }[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: { thread: ClientElement<"thread">; }, index: number, obj: { thread: ClientElement<"thread">; }[]) => unknown, thisArg?: any): { thread: ClientElement<"thread">; } | undefined; }`

- `findIndex`: `(predicate: (value: { thread: ClientElement<"thread">; }, index: number, obj: { thread: ClientElement<"thread">; }[]) => unknown, thisArg?: any) => number`

- `fill`: `(value: { thread: ClientElement<"thread">; }, start?: number, end?: number) => [{ thread: ClientElement<"thread">; }]`

- `copyWithin`: `(target: number, start: number, end?: number) => [{ thread: ClientElement<"thread">; }]`

- `entries`: `() => ArrayIterator<[number, { thread: ClientElement<"thread">; }]>`

- `keys`: `() => ArrayIterator<number>`

- `values`: `() => ArrayIterator<{ thread: ClientElement<"thread">; }>`

- `includes`: `(searchElement: { thread: ClientElement<"thread">; }, fromIndex?: number) => boolean`

- `flatMap`: `<U, This>(callback: (this: This, value: { thread: ClientElement<"thread">; }, index: number, array: { thread: ClientElement<"thread">; }[]) => U | readonly U[], thisArg?: This | undefined) => U[]`

- `flat`: `<A, D>(this: A, depth?: D | undefined) => FlatArray<A, D>[]`

- `at`: `(index: number) => { thread: ClientElement<"thread">; }`

- `findLast`: `{ <S>(predicate: (value: { thread: ClientElement<"thread">; }, index: number, array: { thread: ClientElement<"thread">; }[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: { thread: ClientElement<"thread">; }, index: number, array: { thread: ClientElement<"thread">; }[]) => unknown, thisArg?: any): { thread: ClientElement<"thread">; } | undefined; }`

- `findLastIndex`: `(predicate: (value: { thread: ClientElement<"thread">; }, index: number, array: { thread: ClientElement<"thread">; }[]) => unknown, thisArg?: any) => number`

- `toReversed`: `() => { thread: ClientElement<"thread">; }[]`

- `toSorted`: `(compareFn?: ((a: { thread: ClientElement<"thread">; }, b: { thread: ClientElement<"thread">; }) => number) | undefined) => { thread: ClientElement<"thread">; }[]`

- `toSpliced`: `{ (start: number, deleteCount: number, ...items: { thread: ClientElement<"thread">; }[]): { thread: ClientElement<"thread">; }[]; (start: number, deleteCount?: number): { thread: ClientElement<"thread">; }[]; }`

- `with`: `(index: number, value: { thread: ClientElement<"thread">; }) => { thread: ClientElement<"thread">; }[]`

### Suggestions

- `0?`: `SuggestionConfig[]`
- `length`: `0 | 1`
- `toString`: `() => string`
- `toLocaleString`: `{ (): string; (locales: string | string[], options?: Intl.NumberFormatOptions & Intl.DateTimeFormatOptions): string; }`
- `pop`: `() => SuggestionConfig[]`
- `push`: `(...items: (SuggestionConfig[] | undefined)[]) => number`
- `concat`: `{ (...items: ConcatArray<SuggestionConfig[] | undefined>[]): (SuggestionConfig[] | undefined)[]; (...items: (SuggestionConfig[] | ConcatArray<SuggestionConfig[] | undefined> | undefined)[]): (SuggestionConfig[] | undefined)[]; }`
- `join`: `(separator?: string) => string`
- `reverse`: `() => (SuggestionConfig[] | undefined)[]`
- `shift`: `() => SuggestionConfig[]`
- `slice`: `(start?: number, end?: number) => (SuggestionConfig[] | undefined)[]`
- `sort`: `(compareFn?: ((a: SuggestionConfig[] | undefined, b: SuggestionConfig[] | undefined) => number) | undefined) => [suggestions?: SuggestionConfig[] | undefined]`
- `splice`: `{ (start: number, deleteCount?: number): (SuggestionConfig[] | undefined)[]; (start: number, deleteCount: number, ...items: (SuggestionConfig[] | undefined)[]): (SuggestionConfig[] | undefined)[]; }`
- `unshift`: `(...items: (SuggestionConfig[] | undefined)[]) => number`
- `indexOf`: `(searchElement: SuggestionConfig[] | undefined, fromIndex?: number) => number`
- `lastIndexOf`: `(searchElement: SuggestionConfig[] | undefined, fromIndex?: number) => number`
- `every`: `{ <S>(predicate: (value: SuggestionConfig[] | undefined, index: number, array: (SuggestionConfig[] | undefined)[]) => value is S, thisArg?: any): this is S[]; (predicate: (value: SuggestionConfig[] | undefined, index: number, array: (SuggestionConfig[] | undefined)[]) => unknown, thisArg?: any): boolean; }`
- `some`: `(predicate: (value: SuggestionConfig[] | undefined, index: number, array: (SuggestionConfig[] | undefined)[]) => unknown, thisArg?: any) => boolean`
- `forEach`: `(callbackfn: (value: SuggestionConfig[] | undefined, index: number, array: (SuggestionConfig[] | undefined)[]) => void, thisArg?: any) => void`
- `map`: `<U>(callbackfn: (value: SuggestionConfig[] | undefined, index: number, array: (SuggestionConfig[] | undefined)[]) => U, thisArg?: any) => U[]`
- `filter`: `{ <S>(predicate: (value: SuggestionConfig[] | undefined, index: number, array: (SuggestionConfig[] | undefined)[]) => value is S, thisArg?: any): S[]; (predicate: (value: SuggestionConfig[] | undefined, index: number, array: (SuggestionConfig[] | undefined)[]) => unknown, thisArg?: any): (SuggestionConfig[] | undefined)[]; }`
- `reduce`: `{ (callbackfn: (previousValue: SuggestionConfig[] | undefined, currentValue: SuggestionConfig[] | undefined, currentIndex: number, array: (SuggestionConfig[] | undefined)[]) => SuggestionConfig[] | undefined): SuggestionConfig[] | undefined; (callbackfn: (previousValue: SuggestionConfig[] | undefined, currentValue: SuggestionConfig[] | undefined, currentIndex: number, array: (SuggestionConfig[] | undefined)[]) => SuggestionConfig[] | undefined, initialValue: SuggestionConfig[] | undefined): SuggestionConfig[] | undefined; <U>(callbackfn: (previousValue: U, currentValue: SuggestionConfig[] | undefined, currentIndex: number, array: (SuggestionConfig[] | undefined)[]) => U, initialValue: U): U; }`
- `reduceRight`: `{ (callbackfn: (previousValue: SuggestionConfig[] | undefined, currentValue: SuggestionConfig[] | undefined, currentIndex: number, array: (SuggestionConfig[] | undefined)[]) => SuggestionConfig[] | undefined): SuggestionConfig[] | undefined; (callbackfn: (previousValue: SuggestionConfig[] | undefined, currentValue: SuggestionConfig[] | undefined, currentIndex: number, array: (SuggestionConfig[] | undefined)[]) => SuggestionConfig[] | undefined, initialValue: SuggestionConfig[] | undefined): SuggestionConfig[] | undefined; <U>(callbackfn: (previousValue: U, currentValue: SuggestionConfig[] | undefined, currentIndex: number, array: (SuggestionConfig[] | undefined)[]) => U, initialValue: U): U; }`
- `find`: `{ <S>(predicate: (value: SuggestionConfig[] | undefined, index: number, obj: (SuggestionConfig[] | undefined)[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: SuggestionConfig[] | undefined, index: number, obj: (SuggestionConfig[] | undefined)[]) => unknown, thisArg?: any): SuggestionConfig[] | undefined; }`
- `findIndex`: `(predicate: (value: SuggestionConfig[] | undefined, index: number, obj: (SuggestionConfig[] | undefined)[]) => unknown, thisArg?: any) => number`
- `fill`: `(value: SuggestionConfig[] | undefined, start?: number, end?: number) => [suggestions?: SuggestionConfig[] | undefined]`
- `copyWithin`: `(target: number, start: number, end?: number) => [suggestions?: SuggestionConfig[] | undefined]`
- `entries`: `() => ArrayIterator<[number, SuggestionConfig[] | undefined]>`
- `keys`: `() => ArrayIterator<number>`
- `values`: `() => ArrayIterator<SuggestionConfig[] | undefined>`
- `includes`: `(searchElement: SuggestionConfig[] | undefined, fromIndex?: number) => boolean`
- `flatMap`: `<U, This>(callback: (this: This, value: SuggestionConfig[] | undefined, index: number, array: (SuggestionConfig[] | undefined)[]) => U | readonly U[], thisArg?: This | undefined) => U[]`
- `flat`: `<A, D>(this: A, depth?: D | undefined) => FlatArray<A, D>[]`
- `at`: `(index: number) => SuggestionConfig[]`
- `findLast`: `{ <S>(predicate: (value: SuggestionConfig[] | undefined, index: number, array: (SuggestionConfig[] | undefined)[]) => value is S, thisArg?: any): S | undefined; (predicate: (value: SuggestionConfig[] | undefined, index: number, array: (SuggestionConfig[] | undefined)[]) => unknown, thisArg?: any): SuggestionConfig[] | undefined; }`
- `findLastIndex`: `(predicate: (value: SuggestionConfig[] | undefined, index: number, array: (SuggestionConfig[] | undefined)[]) => unknown, thisArg?: any) => number`
- `toReversed`: `() => (SuggestionConfig[] | undefined)[]`
- `toSorted`: `(compareFn?: ((a: SuggestionConfig[] | undefined, b: SuggestionConfig[] | undefined) => number) | undefined) => (SuggestionConfig[] | undefined)[]`
- `toSpliced`: `{ (start: number, deleteCount: number, ...items: (SuggestionConfig[] | undefined)[]): (SuggestionConfig[] | undefined)[]; (start: number, deleteCount?: number): (SuggestionConfig[] | undefined)[]; }`
- `with`: `(index: number, value: SuggestionConfig[] | undefined) => (SuggestionConfig[] | undefined)[]`

### useSmooth

Animates streamed message part text with a typewriter-style reveal.

Takes the current part state and a `smooth` argument: `false` disables, `true` uses the default rate, and a SmoothOptions object tunes the reveal. Returns the part state with `text` replaced by the revealed prefix and `status` reporting `running` until the reveal catches up. If the source settles before any character has been revealed, the text is committed immediately so a missed animation frame cannot leave an empty bubble.

The reveal auto-disables under `prefers-reduced-motion: reduce`, committing the full text immediately; this takes precedence over an explicit `smooth={true}`.

```
const { text, status } = useSmooth(useMessagePartText(), {
  drainMs: 500,
  maxCharsPerFrame: 30,
});
```

- `state`: `(TextMessagePart & { readonly status: MessagePartStatus | ToolCallMessagePartStatus; }) | (ReasoningMessagePart & { readonly status: MessagePartStatus | ToolCallMessagePartStatus; })`

  - `type`: `"text"`
  - `id?`: `string` — A stable identity the host supplies for this part, used as its render and store key when non-empty and no other part of the same type in the message shares it; otherwise the part is keyed by its type and position.
  - `text`: `string`
  - `status`: `MessagePartStreamStatus`
    - `type`: `"running"`
  - `providerMetadata?`: `PartProviderMetadata`
  - `parentId?`: `string`

- `smooth?`: `boolean | SmoothOptions`

### fromThreadMessageLike

> [!tip]
>
> **Experimental.** Shipped 2024-07-25; it may change in any release.

```
const fromThreadMessageLike: (like: ThreadMessageLike, fallbackId: string, fallbackStatus: MessageStatus) => ThreadMessage;
```

### generateId

> [!tip]
>
> **Experimental.** Shipped 2024-05-31; it may change in any release.

```
const generateId: (size?: number) => string;
```

### unstable_defaultWebMcpFilter

> [!tip]
>
> **Experimental.** Shipped 2026-08-29; it may change in any release.

The predicate the WebMCP provider uses when no `filter` is passed: an enabled frontend tool with a client-side `execute`. A tool authored without a `type` is included, because `execute` is what distinguishes the deprecated type-less form from a backend or human tool. A `filter` replaces this, so pass it through to narrow the default set rather than widen it.

```
const unstable_defaultWebMcpFilter: (_name: string, tool: Tool<any, any>) => boolean;
```

### unstable_useWebMcpProvider

> [!tip]
>
> **Experimental.** Shipped 2026-08-29; it may change in any release.

Publishes the frontend tools in the model context to a WebMCP-capable browser, so the user's own browser agent can call them.

Returns `status: "unsupported"` when the page exposes no `document.modelContext` (or `navigator.modelContext`), and the sorted names of the tools currently registered with the host.

- `filter?`: `(name: string, tool: Tool<any, any>) => boolean`

### unstable_defaultDirectiveFormatter

> [!warn]
>
> **Deprecated.** Use `defaultDirectiveFormatter` instead.

```
const unstable_defaultDirectiveFormatter: DirectiveFormatter;
```