---
id: useQuery
title: useQuery
---

```tsx
const {
	data,
	dataUpdatedAt,
	error,
	errorUpdateCount,
	errorUpdatedAt,
	failureCount,
	failureReason,
	fetchStatus,
	isError,
	isFetched,
	isFetchedAfterMount,
	isFetching,
	isInitialLoading,
	isLoading,
	isLoadingError,
	isPaused,
	isPlaceholderData,
	isPreviousData,
	isRefetchError,
	isRefetching,
	isStale,
	isSuccess,
	refetch,
	remove,
	status
} = useQuery({
  queryKey,
  queryFn,
  cacheTime,
  enabled,
  networkMode,
  initialData,
  initialDataUpdatedAt,
  keepPreviousData,
  meta,
  notifyOnChangeProps,
  onError,
  onSettled,
  onSuccess,
  placeholderData,
  queryKeyHashFn,
  refetchInterval,
  refetchIntervalInBackground,
  refetchOnMount,
  refetchOnReconnect,
  refetchOnWindowFocus,
  retry,
  retryOnMount,
  retryDelay,
  select,
  staleTime,
  structuralSharing,
  suspense,
  useErrorBoundary,
})
```

**选项**

- `queryKey: unknown[]`
  - **必需**
  - 用于此查询的查询键。
  - 查询键将被散列成稳定的哈希。有关更多信息，请参见 [查询键](../guides/query-keys)。
  - 当此键发生更改时，查询将自动更新（只要 `enabled` 未设置为 `false`）。
- `queryFn: (context: QueryFunctionContext) => Promise<TData>`
  - **必需，但仅当没有定义默认查询函数时**
  - 有关更多信息，请参见 [默认查询函数](../guides/default-query-function)。
  - 用于请求数据的查询将使用的函数。
  - 接收一个 [QueryFunctionContext](../guides/query-functions#queryfunctioncontext)
  - 必须返回一个要么解析数据要么抛出错误的承诺。数据不能为 `undefined`。
- `enabled: boolean`
  - 将此设置为 `false` 以禁用自动运行此查询。
  - 可用于[依赖查询](../guides/dependent-queries)。
- `networkMode: 'online' | 'always' | 'offlineFirst`
  - 可选
  - 默认为 `'online'`
  - 有关更多信息，请参见 [网络模式](../guides/network-mode)。
- `retry: boolean | number | (failureCount: number, error: TError) => boolean`
  - 如果为 `false`，默认情况下查询失败不会重试。
  - 如果为 `true`，默认情况下查询失败将无限重试。
  - 如果设置为一个数字，例如 `3`，则默认情况下查询失败将重试，直到失败的查询计数达到该数字。
- `retryOnMount: boolean`
  - 如果设置为 `false`，如果查询包含错误，则查询不会在挂载时重试。默认为 `true`。
- `retryDelay: number | (retryAttempt: number, error: TError) => number`
  - 此函数接收一个 `retryAttempt` 整数和实际错误，并返回下一次尝试之前应用的延迟时间（以毫秒为单位）。
  - 类似于 `attempt => Math.min(attempt > 1 ? 2 ** attempt * 1000 : 1000, 30 * 1000)` 的函数应用指数退避。
  - 类似于 `attempt => attempt * 1000` 的函数应用线性退避。
- `staleTime: number | Infinity`
  - 可选
  - 默认为 `0`
  - 数据被视为陈旧的时间（以毫秒为单位）。此值仅适用于定义它的钩子。
  - 如果设置为 `Infinity`，数据将永远不会被视为陈旧。
- `cacheTime: number | Infinity`
  - 默认为 `5 * 60 * 1000`（5 分钟）或在 SSR 期间为 `Infinity`
  - 未使用/不活动的缓存数据在内存中保留的时间（以毫秒为单位）。当查询的缓存变为未使用或不活动状态时，该缓存数据将在此持续时间后被垃圾回收。当指定不同的缓存时间时，将使用最长的时间。
  - 如果设置为 `Infinity`，将禁用垃圾回收
- `queryKeyHashFn: (queryKey: QueryKey) => string`
  - 可选
  - 如果指定，将使用此函数将 `queryKey` 散列为字符串。
- `refetchInterval: number | false | ((data: TData | undefined, query: Query) => number | false)`
  - 可选
  - 如果设置为数字，则所有查询将以此频率（以毫秒为单位）连续重新获取
  - 如果设置为函数，则将使用最新数据和查询来计算频率
- `refetchIntervalInBackground: boolean`
  - 可选
  - 如果设置为 `true`，则设置为以 `refetchInterval` 连续重新获取的查询将在标签/窗口在后台时继续重新获取
- `refetchOnMount: boolean | "always" | ((query: Query) => boolean | "always")`
  - 可选
  - 默认为 `true`
  - 如果设置为 `true`，如果数据陈旧，则查询将在挂载时重新获取。
  - 如果设置为 `false`，则查询将不会在挂载时重新获取。
  - 如果设置为 `"always"`，则查询将始终在挂载时重新获取。
  - 如果设置为函数，则将使用查询来计算值
- `refetchOnWindowFocus: boolean | "always" | ((query: Query) => boolean | "always")`
  - 可选
  - 默认为 `true`
  - 如果设置为 `true`，如果数据陈旧，则查询将在窗口获得焦点时重新获取。
  - 如果设置为 `false`，则查询不会在窗口获得焦点时重新获取。
  - 如果设置为 `"always"`，则查询将始终在窗口获得焦点时重新获取。
  - 如果设置为函数，则将使用查询来计算值
- `ref

etchOnReconnect: boolean | "always" | ((query: Query) => boolean | "always")`
- 可选
- 默认为 `true`
- 如果设置为 `true`，如果数据陈旧，则查询将在重新连接时重新获取。
- 如果设置为 `false`，则查询将不会在重新连接时重新获取。
- 如果设置为 `"always"`，则查询将始终在重新连接时重新获取。
- 如果设置为函数，则将使用查询来计算值
- `notifyOnChangeProps: string[] | "all" | (() => string[] | "all")`
  - 可选
  - 如果设置，只有列出的属性更改时组件才会重新渲染。
  - 例如，如果将其设置为 `['data', 'error']`，则组件仅在 `data` 或 `error` 属性更改时重新渲染。
  - 如果设置为 `"all"`，则组件将退出智能跟踪，并在每次更新查询时重新渲染。
  - 如果设置为函数，将执行该函数以计算属性列表。
  - 默认情况下，将跟踪属性的访问，并且组件仅在跟踪的属性之一更改时重新渲染。
- `onSuccess: (data: TData) => void`
  - **已弃用** - 此回调将在下一个主要版本中删除
  - 可选
  - 该函数将在查询成功获取新数据时触发。
- `onError: (error: TError) => void`
  - **已弃用** - 此回调将在下一个主要版本中删除
  - 可选
  - 如果查询遇到错误，此函数将触发，并传递错误。
- `onSettled: (data?: TData, error?: TError) => void`
  - **已弃用** - 此回调将在下一个主要版本中删除
  - 可选
  - 此函数将在查询成功获取或错误时触发，并传递数据或错误。
- `select: (data: TData) => unknown`
  - 可选
  - 此选项可用于转换或选择查询函数返回的数据的一部分。它会影响返回的 `data` 值，但不会影响存储在查询缓存中的内容。
- `suspense: boolean`
  - 可选
  - 将此设置为 `true` 以启用 suspense 模式。
  - 当为 `true` 时，当 `status === 'loading'` 时，`useQuery` 将暂停。
  - 当为 `true` 时，当 `status === 'error'` 时，`useQuery` 将抛出运行时错误。
- `initialData: TData | () => TData`
  - 可选
  - 如果设置，将使用此值作为查询缓存的初始数据（只要尚未创建或缓存查询）
  - 如果设置为函数，则将在共享/根查询初始化期间调用该函数**一次**，并且预期同步返回 initialData
  - 默认情况下，初始数据被视为陈旧，除非设置了 `staleTime`。
  - `initialData` **被持久化**到缓存中
- `initialDataUpdatedAt: number | (() => number | undefined)`
  - 可选
  - 如果设置，将使用此值作为 `initialData` 本身最后更新的时间（以毫秒为单位）。
- `placeholderData: TData | () => TData`
  - 可选
  - 如果设置，将使用此值作为此特定查询观察器的占位符数据，而查询仍处于 `loading` 数据状态且尚未提供 initialData。
  - `placeholderData` **不会被持久化**到缓存中
- `keepPreviousData: boolean`
  - 可选
  - 默认为 `false`
  - 如果设置，当获取新数据时，将保留任何先前的 `data`，因为查询键已更改。
- `isDataEqual: (oldData: TData | undefined, newData: TData) => boolean`
  - **已弃用**。您可以通过将函数传递给 `structuralSharing` 来实现相同的功能：
    - structuralSharing: (oldData, newData) => isDataEqual(oldData, newData) ? oldData : replaceEqualDeep(oldData, newData)
  - 可选
  - 此函数应返回布尔值，指示是使用先前的 `data` (`true`) 还是使用新数据 (`false`) 作为查询的已解析数据。
- `structuralSharing: boolean | ((oldData: TData | undefined, newData: TData) => TData)`
  - 可选
  - 默认为 `true`
  - 如果设置为 `false`，将禁用查询结果之间的结构共享。
  - 如果设置为函数，将通过此函数传递旧数据和新数据值，该函数应将它们合并到查询的已解析数据中。这样，即使数据包含不可序列化的值，您也可以保留对旧数据的引用，以提高性能。
- `useErrorBoundary: undefined | boolean | (error: TError, query: Query) => boolean`
  - 默认为全局查询配置的 `useErrorBoundary` 值，即 `undefined`
  - 如果要在渲染阶段抛出错误并传播到最近的错误边界，请将此设置为 `true`
  - 如果要禁用 `suspense` 的默认行为，将错误抛出到错误边界，请将此设置为 `false`
  - 如果设置为函数，将传递错误和查询，它应该返回一个布尔值，指示是在错误边界中显示错误（`true`）还是将错误作为状态返回（`false`）
- `meta: Record<string, unknown>`
  - 可选
  - 如果设置，将附加信息存储在查询缓存条目上，根据需要使用。它将在查询可用的任何地方都可访问，并且还是提供给 `queryFn` 的 `QueryFunctionContext`

的一部分。
- `context?: React.Context<QueryClient | undefined>`
  - 使用此选项来使用自定义的 React Query 上下文。否则，将使用 `defaultContext`。

**返回值**

- `status: String`
  - 将为：
    - 如果没有缓存数据且尚未完成查询尝试，则为 `loading`。
    - 如果查询尝试导致错误，则为 `error`。对应的 `error` 属性具有从尝试的获取中收到的错误。
    - 如果查询收到没有错误的响应并准备显示其数据，则为 `success`。查询上的对应 `data` 属性是从成功的获取中收到的数据，或者如果查询的 `enabled` 属性设置为 `false` 且尚未获取过，则 `data` 是在初始化期间对查询提供的第一个 `initialData`。
- `isLoading: boolean`
  - 这是从上述 `status` 变量派生出的布尔值，以方便使用。
- `isSuccess: boolean`
  - 这是从上述 `status` 变量派生出的布尔值，以方便使用。
- `isError: boolean`
  - 这是从上述 `status` 变量派生出的布尔值，以方便使用。
- `isLoadingError: boolean`
  - 如果首次获取数据时查询失败，则为 `true`。
- `isRefetchError: boolean`
  - 如果重新获取时查询失败，则为 `true`。
- `data: TData`
  - 默认为 `undefined`。
  - 查询的上次成功解析数据。
- `dataUpdatedAt: number`
  - 当查询最近将 `status` 返回为 `"success"` 时的时间戳。
- `error: null | TError`
  - 默认为 `null`
  - 查询的错误对象，如果发生错误则会传递错误。
- `errorUpdatedAt: number`
  - 当查询最近将 `status` 返回为 `"error"` 时的时间戳。
- `isStale: boolean`
  - 如果缓存中的数据无效或数据早于给定的 `staleTime`，则为 `true`。
- `isPlaceholderData: boolean`
  - 如果显示的数据是占位符数据，则为 `true`。
- `isPreviousData: boolean`
  - 当设置了 `keepPreviousData` 并返回先前查询的数据时，为 `true`。
- `isFetched: boolean`
  - 如果查询已获取，则为 `true`。
- `isFetchedAfterMount: boolean`
  - 如果组件挂载后查询已获取，则为 `true`。
  - 此属性可用于不显示任何先前缓存的数据。
- `fetchStatus: FetchStatus`
  - `fetching`：每当查询函数正在执行时为 `true`，这包括初始 `loading` 以及后台重新获取。
  - `paused`：查询想要获取，但已被 `paused`。
  - `idle`：查询不在获取。
  - 有关更多信息，请参见 [Network Mode](../guides/network-mode)。
- `isFetching: boolean`
  - 这是从上述 `fetchStatus` 变量派生出的布尔值，以方便使用。
- `isPaused: boolean`
  - 这是从上述 `fetchStatus` 变量派生出的布尔值，以方便使用。
- `isRefetching: boolean`
  - 当后台重新获取正在进行时为 `true`，这 _不包括_ 初始 `loading`
  - 与 `isFetching && !isLoading` 相同
- `isInitialLoading: boolean`
  - 当查询的第一个获取正在进行时为 `true`
  - 与 `isFetching && isLoading` 相同
- `failureCount: number`
  - 查询的失败计数。
  - 每次查询失败时递增。
  - 成功时重置为 `0`。
- `failureReason: null | TError`
  - 查询重试的失败原因。
  - 成功时重置为 `null`。
- `errorUpdateCount: number`
  - 所有错误的总和。
- `refetch: (options: { throwOnError: boolean, cancelRefetch: boolean }) => Promise<UseQueryResult>`
  - 一个手动重新获取查询的函数。
  - 如果查询错误，错误仅会被记录。如果要抛出错误，请传递 `throwOnError: true` 选项
  - `cancelRefetch?: boolean`
    - 默认为 `true`
      - 默认情况下，将在进行新请求之前取消当前正在运行的请求
    - 当设置为 `false` 时，如果已经有一个请求正在运行，则不会进行重新获取。
- `remove: () => void`
  - 从缓存中删除查询的函数。
