---
id: useMutation
title: useMutation
---

```tsx
const {
  data,
  error,
  isError,
  isIdle,
  isLoading,
  isPaused,
  isSuccess,
  failureCount,
  failureReason,
  mutate,
  mutateAsync,
  reset,
  status,
} = useMutation({
  mutationFn,
  cacheTime,
  mutationKey,
  networkMode,
  onError,
  onMutate,
  onSettled,
  onSuccess,
  retry,
  retryDelay,
  useErrorBoundary,
  meta
})

mutate(variables, {
  onError,
  onSettled,
  onSuccess,
})
```

**选项**

- `mutationFn: (variables: TVariables) => Promise<TData>`
  - **必填，但仅在未定义默认突变函数时需要**
  - 执行异步任务并返回 promise 的函数。
  - `variables` 是 `mutate` 将传递给你的 `mutationFn` 的对象。
- `cacheTime: number | Infinity`
  - 未使用/非活动缓存数据在内存中保留的时间（以毫秒为单位）。当突变的缓存未被使用或非活动时，缓存数据将在此持续时间后进行垃圾回收。当指定不同的缓存时间时，将使用最长的时间。
  - 如果设置为 `Infinity`，将禁用垃圾回收
- `mutationKey: unknown[]`
  - 可选
  - 突变键可以设置为继承使用 `queryClient.setMutationDefaults` 设置的默认值。
- `networkMode: 'online' | 'always' | 'offlineFirst`
  - 可选
  - 默认为 `'online'`
  - 更多信息请参见[网络模式](../guides/network-mode)。
- `onMutate: (variables: TVariables) => Promise<TContext | void> | TContext | void`
  - 可选
  - 此函数将在突变函数执行之前触发，并传递与突变函数将接收的相同变量。
  - 用于在希望突变成功的情况下对资源进行乐观更新。
  - 此函数返回的值将在突变失败时传递给 `onError` 和 `onSettled` 函数，并且在回滚乐观更新时可能会有用。
- `onSuccess: (data: TData, variables: TVariables, context?: TContext) => Promise<unknown> | unknown`
  - 可选
  - 当突变成功时，此函数将被触发，并传递突变的结果。
  - 如果返回一个 promise，它将在继续之前等待和解析
- `onError: (err: TError, variables: TVariables, context?: TContext) => Promise<unknown> | unknown`
  - 可选
  - 当突变遇到错误时，此函数将被触发，并传递错误。
  - 如果返回一个 promise，它将在继续之前等待和解析
- `onSettled: (data: TData, error: TError, variables: TVariables, context?: TContext) => Promise<unknown> | unknown`
  - 可选
  - 当突变成功获取或遇到错误时，此函数将被触发，并传递数据或错误。
  - 如果返回一个 promise，它将在继续之前等待和解析
- `retry: boolean | number | (failureCount: number, error: TError) => boolean`
  - 默认为 `0`。
  - 如果为 `false`，失败的突变不会重试。
  - 如果为 `true`，失败的突变将无限重试。
  - 如果设置为数字，例如 `3`，失败的突变将重试，直到失败的突变计数达到该数字。
- `retryDelay: number | (retryAttempt: number, error: TError) => number`
  - 此函数接收 `retryAttempt` 整数和实际的错误，并返回在下一次尝试之前应用的延迟，单位为毫秒。
  - 像 `attempt => Math.min(attempt > 1 ? 2 ** attempt * 1000 : 1000, 30 * 1000)` 这样的函数会应用指数回退。
  - 像 `attempt => attempt * 1000` 这样的函数会应用线性回退。
- `useErrorBoundary: undefined | boolean | (error: TError) => boolean`
  - 默认为全局查询配置的 `useErrorBoundary` 值，即 `undefined`
  - 如果希望突变错误在渲染阶段抛出并传播到最近的错误边界，请将其设置为 `true`
  - 如果希望禁用将错误抛到错误边界的行为，请将其设置为 `false`。
  - 如果设置为函数，它将传递错误，并应返回一个布尔值，指示是否在错误边界中显示错误（`true`）或将错误作为状态返回（`false`）
- `meta: Record<string, unknown>`
  - 可选
  - 如果设置，将在突变缓存条目上存储额外的信息，可根据需要使用。它将在 `mutation` 可用的任何地方都可以访问（例如，`MutationCache` 的 `onError`、`onSuccess` 函数中）。
- `context?: React.Context<QueryClient | undefined>`
  - 使用此选项使用自定义的 React Query 上下文。否则，将使用 `defaultContext`。

**返回值**

- `mutate: (variables: TVariables, { onSuccess, onSettled, onError }) => void`
  - 你可以通过传递变量调用此突变函数，以触发突变，并可选地在其他回调选项上触发钩子。
  - `variables: TVariables`
    - 可选
    - 要传递给 `mutationFn` 的变量对象。
  - `onSuccess: (data: TData, variables: TVariables, context: TContext) => void`
    - 可选
    - 当突变成功时，此函数将被触发，并传递突变的结果。
    - 空函数，返回

值将被忽略
- `onError: (err: TError, variables: TVariables, context: TContext | undefined) => void`
  - 可选
  - 当突变遇到错误时，此函数将被触发，并传递错误。
  - 空函数，返回值将被忽略
- `onSettled: (data: TData | undefined, error: TError | null, variables: TVariables, context: TContext | undefined) => void`
  - 可选
  - 当突变成功获取或遇到错误时，此函数将被触发，并传递数据或错误。
  - 空函数，返回值将被忽略
- 如果进行多次请求，`onSuccess` 仅会在你最新的调用之后触发。
- `mutateAsync: (variables: TVariables, { onSuccess, onSettled, onError }) => Promise<TData>`
  - 类似于 `mutate`，但返回一个可以等待的 promise。
- `status: string`
  - 将是：
    - `idle`：在突变函数执行之前的初始状态。
    - `loading`：如果突变正在执行。
    - `error`：如果上次突变尝试出现错误。
    - `success`：如果上次突变尝试成功。
- `isIdle`、`isLoading`、`isSuccess`、`isError`：从 `status` 派生的布尔变量
- `isPaused: boolean`
  - 如果突变已被`暂停`，将为`true`
  - 更多信息请参见[网络模式](../guides/network-mode)。
- `data: undefined | unknown`
  - 默认为 `undefined`
  - 查询的上次成功解析的数据。
- `error: null | TError`
  - 查询的错误对象，如果出现错误。
- `reset: () => void`
  - 一个函数，用于清除突变的内部状态（即，将突变重置为其初始状态）。
- `failureCount: number`
  - 突变的失败计数。
  - 每次突变失败时递增。
  - 当突变成功时重置为 `0`。
- `failureReason: null | TError`
  - 突变重试的失败原因。
  - 当突变成功时重置为 `null`。
