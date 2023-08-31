---
id: QueryClient
title: 查询客户端
---

## `QueryClient`

`QueryClient`可以用于与cache进行交互：

```tsx
import { QueryClient } from '@tanstack/react-query'

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: Infinity,
    },
  },
})

await queryClient.prefetchQuery({ queryKey: ['posts'], queryFn: fetchPosts })
```

它的可用方法包括：

- [`queryClient.fetchQuery`](#queryclientfetchquery)
- [`queryClient.fetchInfiniteQuery`](#queryclientfetchinfinitequery)
- [`queryClient.prefetchQuery`](#queryclientprefetchquery)
- [`queryClient.prefetchInfiniteQuery`](#queryclientprefetchinfinitequery)
- [`queryClient.getQueryData`](#queryclientgetquerydata)
- [`queryClient.ensureQueryData`](#queryclientensurequerydata)
- [`queryClient.getQueriesData`](#queryclientgetqueriesdata)
- [`queryClient.setQueryData`](#queryclientsetquerydata)
- [`queryClient.getQueryState`](#queryclientgetquerystate)
- [`queryClient.setQueriesData`](#queryclientsetqueriesdata)
- [`queryClient.invalidateQueries`](#queryclientinvalidatequeries)
- [`queryClient.refetchQueries`](#queryclientrefetchqueries)
- [`queryClient.cancelQueries`](#queryclientcancelqueries)
- [`queryClient.removeQueries`](#queryclientremovequeries)
- [`queryClient.resetQueries`](#queryclientresetqueries)
- [`queryClient.isFetching`](#queryclientisfetching)
- [`queryClient.isMutating`](#queryclientismutating)
- [`queryClient.getLogger`](#queryclientgetlogger)
- [`queryClient.getDefaultOptions`](#queryclientgetdefaultoptions)
- [`queryClient.setDefaultOptions`](#queryclientsetdefaultoptions)
- [`queryClient.getQueryDefaults`](#queryclientgetquerydefaults)
- [`queryClient.setQueryDefaults`](#queryclientsetquerydefaults)
- [`queryClient.getMutationDefaults`](#queryclientgetmutationdefaults)
- [`queryClient.setMutationDefaults`](#queryclientsetmutationdefaults)
- [`queryClient.getQueryCache`](#queryclientgetquerycache)
- [`queryClient.getMutationCache`](#queryclientgetmutationcache)
- [`queryClient.clear`](#queryclientclear)
- [`queryClient.resumePausedMutations`](#queryclientresumepausedmutations)

**选项**

- `queryCache?: QueryCache`
  - 可选
  - 与该客户端连接的查询缓存。
- `mutationCache?: MutationCache`
  - 可选
  - 与该客户端连接的突变缓存。
- `logger?: Logger`
  - 可选
  - 该客户端用于记录调试信息、警告和错误的记录器。如果未设置，将使用 `console` 作为默认记录器。
- `defaultOptions?: DefaultOptions`
  - 可选
  - 使用该查询客户端的所有查询和突变的默认值。

## `queryClient.fetchQuery`

`fetchQuery` 是一个异步方法，用于获取和缓存查询。它要么返回数据，要么抛出错误。如果只想获取查询结果而不需要结果，请使用 `prefetchQuery` 方法。

如果查询存在且数据未失效或早于给定的 `staleTime`，则将从缓存返回数据。否则，它将尝试获取最新数据。

> 使用 `fetchQuery` 和 `setQueryData` 的区别在于 `fetchQuery` 是异步的，并且会确保在数据获取期间呈现同一查询的 `useQuery` 实例时不会创建重复的请求。

```tsx
try {
  const data = await queryClient.fetchQuery({ queryKey, queryFn })
} catch (error) {
  console.log(error)
}
```

指定 `staleTime` 以仅在数据过时一定时间后才获取：

```tsx
try {
  const data = await queryClient.fetchQuery({ queryKey, queryFn, staleTime: 10000 })
} catch (error) {
  console.log(error)
}
```

**选项**

`fetchQuery` 的选项与 [`useQuery`](../reference/useQuery) 完全相同，除了以下选项：`enabled, refetchInterval, refetchIntervalInBackground, refetchOnWindowFocus, refetchOnReconnect, notifyOnChangeProps, onSuccess, onError, onSettled, useErrorBoundary, select, suspense, keepPreviousData, placeholderData`；这些选项严格用于 `useQuery` 和 `useInfiniteQuery`。您可以查看[源代码](https://github.com/tannerlinsley/react-query/blob/361935a12cec6f36d0bd6ba12e84136c405047c5/src/core/types.ts#L83)以获取更多信息。

**返回值**

- `Promise<TData>`

## `queryClient.fetchInfiniteQuery`

`fetchInfiniteQuery` 类似于 `fetchQuery`，但可用于获取和缓存无限查询。

```tsx
try {
  const data = await queryClient.fetchInfiniteQuery({ queryKey, queryFn })
  console.log(data.pages)
} catch (error) {
  console.log(error)
}
```

**选项**

`fetchInfiniteQuery` 的选项与 [`fetchQuery`](#queryclientfetchquery) 完全相同。

**返回值**

- `Promise<InfiniteData<TData>>`

## `queryClient.prefetchQuery`

`prefetchQuery` 是一个异步方法，可用于在需要或使用 `useQuery` 等呈现之前预取查询。该方法的工作方式与 `fetchQuery` 相同，只是不会抛出或返回任何数据。

```tsx
await queryClient.prefetchQuery({ queryKey, queryFn })
```

甚至可以在配置中使用默认的 `queryFn` 使用它！

```tsx
await queryClient.prefetchQuery({ queryKey })
```

**选项**

`prefetchQuery` 的选项与 [`fetchQuery`](#queryclientfetchquery) 完全相同。

**返回值**

- `Promise<void>`
  - 该方法返回一个 Promise，如果不需要进行获取，它将立即解析，或在查询执行后解析。它不会返回任何数据或抛出任何错误。

## `queryClient.prefetchInfiniteQuery`

`prefetchInfiniteQuery` 类似于 `prefetchQuery`，但可用于预取和缓存无限查询。

```tsx
await queryClient.prefetchInfiniteQuery({ queryKey, queryFn })
```

**选项**

`prefetchInfiniteQuery` 的选项与 [`fetchQuery`](#queryclientfetchquery) 完全相同。

**返回值**

- `Promise

<void>`
- 该方法返回一个 Promise，如果不需要进行获取，它将立即解析，或在查询执行后解析。它不会返回任何数据或抛出任何错误。

## `queryClient.getQueryData`

`getQueryData` 是一个同步方法，用于从缓存中获取查询数据。

```tsx
const data = queryClient.getQueryData(queryKey)
```

**选项**

- `queryKey: QueryKey`
  - 必须
  - 查询数据的键

**返回值**

- `TData | undefined`
  - 返回查询数据，如果未找到则返回 `undefined`。

## `queryClient.ensureQueryData`

`ensureQueryData` 是一个异步方法，用于从远程数据源获取查询数据，并将其保存在缓存中。

```tsx
await queryClient.ensureQueryData(queryKey, queryFn)
```

**选项**

- `queryKey: QueryKey`
  - 必须
  - 查询数据的键
- `queryFn: QueryFunction<TData>`
  - 必须
  - 用于从远程源获取数据的查询函数

## `queryClient.getQueriesData`

`getQueriesData` 是一个同步方法，用于获取多个查询的数据，类似于 `getQueryData`，但可以一次性获取多个查询的数据。

```tsx
const queriesData = queryClient.getQueriesData([queryKey1, queryKey2])
```

**选项**

- `queryKeys: QueryKey[]`
  - 必须
  - 查询数据的键数组

**返回值**

- `Array<TData | undefined>`
  - 返回一个包含查询数据的数组，如果未找到则返回 `undefined`。

## `queryClient.setQueryData`

`setQueryData` 是一个同步函数，用于立即更新查询的缓存数据。如果查询不存在，它将被创建。**如果查询在默认的 `cacheTime`（5 分钟）内未被查询挂钩利用，则查询将被垃圾回收**。要同时更新多个查询并部分匹配查询键，请使用 [`queryClient.setQueriesData`](#queryclientsetqueriesdata)。

> 使用 `setQueryData` 和 `fetchQuery` 的区别在于 `setQueryData` 是同步的，并且假设您已经同步地有数据可用。如果需要异步获取数据，建议您要么重新获取查询键，要么使用 `fetchQuery` 来处理异步获取。

```tsx
queryClient.setQueryData(queryKey, updater)
```

**选项**

- `queryKey: QueryKey`
- `updater: TQueryFnData | undefined | ((oldData: TQueryFnData | undefined) => TQueryFnData | undefined)`
  - 如果传递非函数的值，数据将更新为该值
  - 如果传递函数，它将接收旧数据值并期望返回一个新值。

**使用更新程序值**

```tsx
setQueryData(queryKey, newData)
```

如果值为 `undefined`，查询数据不会更新。

**使用更新程序函数**

为了简化语法，还可以传递一个更新程序函数，该函数接收当前数据值并返回新值：

```tsx
setQueryData(queryKey, oldData => newData)
```

如果更新程序函数返回 `undefined`，查询数据将不会更新。如果更新程序函数以 `undefined` 作为输入接收，则可以返回 `undefined` 以退出更新，从而不创建新的缓存条目。

**不变性**

通过 `setQueryData` 进行的更新必须以**不可变的**方式执行。**不要**尝试通过在现场变异`oldData`或通过`getQueryData`获取的数据来直接写入缓存。

## `queryClient.getQueryState`

`getQueryState` 是一个同步函数，用于获取现有查询的状态。如果查询不存在，则返回 `undefined`。

```tsx
const state = queryClient.getQueryState({ queryKey })
console.log(state.dataUpdatedAt)
```

**选项**

- `filters?: QueryFilters`
  - 查询过滤器

## `queryClient.setQueriesData`

`setQueriesData` 是一个同步函数，用于通过使用过滤函数或部分匹配查询键立即更新多个查询的缓存数据。仅会更新与传递的查询键或查询过滤器匹配的查询 - 不会创建新的缓存条目。在内部，对每个现有查询都会调用 [`setQueryData`](#queryclientsetquerydata)。

```tsx
queryClient.setQueriesData(filters, updater)
```

**选项**

- `filters: QueryFilters`
  - 如果传递了过滤器，与过滤器匹配的查询键将被更新
- `updater: TQueryFnData | (oldData: TQueryFnData | undefined) => TQueryFnData`
  - [setQueryData](#queryclientsetquerydata) 更新程序函数或新数据，将为每个匹配的查询键调用

## `queryClient.invalidateQueries`

`invalidateQueries` 方法可用于根据查询键或查询的其他功能访问属性/状态使缓存中的单个或多个查询失效并重新获取。默认情况下，所有匹配的查询都会立即标记为无效，并且活动查询会在后台重新获取。

- 如果**不希望活动查询重新获取**，只需使用 `refetchType: 'none'` 选项。
- 如果**希望非活动查询也重新获取**，请使用 `refetchType: 'all'` 选项。

```tsx
await queryClient.invalidateQueries({
  queryKey: ['posts'],
  exact,
  refetchType: 'active',
}, { throwOnError, cancelRefetch })
```

**选项**

- `filters?: QueryFilters`
  - 查询过滤器
  - `queryKey?: QueryKey`
  - `refetchType?: 'active' | 'inactive' | 'all' | 'none'`
    - 默认为 `'active'`
    - 设置为 `active` 时，只有与重新获取谓词匹配且通过 `useQuery` 等渲染的活动查询将在后台重新获取。
    - 设置为 `inactive` 时，只有与重新获取谓词匹配且未通过 `useQuery` 等渲染的非活动查询将在后台重新获取。
    - 设置为 `all` 时，所有与重新获取谓词匹配

的查询将在后台重新获取。
- 设置为 `none` 时，不会重新获取任何查询，仅将与重新获取谓词匹配的查询标记为无效。
- `refetchPage: (page: TData, index: number, allPages: TData[]) => boolean`
  - 仅适用于[无限查询](../guides/infinite-queries#refetchpage)
  - 使用此函数来指定应重新获取哪些页面
- `options?: InvalidateOptions`:
  - `throwOnError?: boolean`
    - 设置为 `true`，如果任何查询重新获取任务失败，此方法将抛出异常。
  - `cancelRefetch?: boolean`
    - 默认为 `true`
      - 默认情况下，在进行新请求之前，当前正在运行的请求将被取消
    - 设置为 `false`，如果已有请求正在运行，则不会进行重新获取。

## `queryClient.refetchQueries`

`refetchQueries` 方法可用于根据某些条件重新获取查询。

示例：

```tsx
// 重新获取所有查询：
await queryClient.refetchQueries()

// 重新获取所有过时的查询：
await queryClient.refetchQueries({ stale: true })

// 重新获取部分匹配查询键的所有活动查询：
await queryClient.refetchQueries({ queryKey: ['posts'], type: 'active' })

// 重新获取完全匹配查询键的所有活动查询：
await queryClient.refetchQueries({ queryKey: ['posts', 1], type: 'active', exact: true })
```

**选项**

- `filters?: QueryFilters`
  - 查询过滤器
  - `refetchPage: (page: TData, index: number, allPages: TData[]) => boolean`
    - 仅适用于[无限查询](../guides/infinite-queries#refetchpage)
    - 使用此函数来指定应重新获取哪些页面
- `options?: RefetchOptions`:
  - `throwOnError?: boolean`
    - 设置为 `true`，如果任何查询重新获取任务失败，此方法将抛出异常。
  - `cancelRefetch?: boolean`
    - 默认为 `true`
      - 默认情况下，在进行新请求之前，当前正在运行的请求将被取消
    - 设置为 `false`，如果已有请求正在运行，则不会进行重新获取。

**返回值**

此函数返回一个承诺，将在所有查询都重新获取完毕时解析。默认情况下，如果这些查询重新获取失败，它**不会**抛出错误，但可以通过将 `throwOnError` 选项设置为 `true` 来进行配置。

## `queryClient.cancelQueries`

`cancelQueries` 方法可用于基于查询键或查询的其他功能访问属性/状态来取消正在进行的查询。

这在执行乐观更新时非常有用，因为您可能需要取消任何正在进行的查询重新获取，以使其不会在解析时覆盖您的乐观更新。

```tsx
await queryClient.cancelQueries({ queryKey: ['posts'], exact: true })
```

**选项**

- `filters?: QueryFilters`
  - 查询过滤器

**返回值**

此方法不返回任何内容。

## `queryClient.removeQueries`

`removeQueries` 方法可用于基于查询键或查询的其他功能访问属性/状态来从缓存中删除查询。

```tsx
queryClient.removeQueries({ queryKey, exact: true })
```

**选项**

- `filters?: QueryFilters`
  - 查询过滤器

**返回值**

此方法不返回任何内容。

## `queryClient.resetQueries`

`resetQueries` 方法可用于根据查询键或查询的其他功能访问属性/状态，将查询重置为其初始状态。

这将通知订阅者 - 与 `clear` 不同，`clear` 将删除所有订阅者 - 并将查询重置为其预加载状态 - 与 `invalidateQueries` 不同。如果查询具有 `initialData`，则查询数据将重置为该值。如果查询处于活动状态，则将重新获取它。

```tsx
queryClient.resetQueries({ queryKey, exact: true })
```

**选项**

- `filters?: QueryFilters`
  - 查询过滤器
  - `refetchPage: (page: TData, index: number, allPages: TData[]) => boolean`
    - 仅适用于[无限查询](../guides/infinite-queries#refetchpage)
    - 使用此函数来指定应重新获取哪些页面
- `options?: ResetOptions`:
  - `throwOnError?: boolean`
    - 设置为 `true`，如果任何查询重新获取任务失败，此方法将抛出异常。
  - `cancelRefetch?: boolean`
    - 默认为 `true`
      - 默认情况下，在进行新请求之前，当前正在运行的请求将被取消
    - 设置为 `false`，如果已有请求正在运行，则不会进行重新获取。

**返回值**

此方法返回一个承诺，将在所有活动查询都重新获取完毕时解析。

## `queryClient.isFetching`

`isFetching` 方法返回一个表示缓存

中是否有正在进行的查询的布尔值。

```tsx
const isFetching = queryClient.isFetching()
console.log(isFetching)
```

## `queryClient.isMutating`

`isMutating` 方法返回一个表示缓存中是否有正在进行的变异的布尔值。

```tsx
const isMutating = queryClient.isMutating()
console.log(isMutating)
```

## `queryClient.getLogger`

`getLogger` 方法用于获取查询客户端当前使用的日志记录器。

```tsx
const logger = queryClient.getLogger()
logger.error('An error occurred')
```

## `queryClient.getDefaultOptions`

`getDefaultOptions` 方法用于获取当前查询客户端的默认选项。

```tsx
const defaultOptions = queryClient.getDefaultOptions()
console.log(defaultOptions)
```

## `queryClient.setDefaultOptions`

`setDefaultOptions` 方法用于设置当前查询客户端的默认选项。

```tsx
queryClient.setDefaultOptions({
  queries: {
    staleTime: 1000 * 60 * 5, // 5 minutes
  },
})
```

**选项**

- `options: DefaultOptions`
  - 必须
  - 新的默认选项

## `queryClient.getQueryDefaults`

`getQueryDefaults` 方法用于获取与指定查询键匹配的默认查询选项。

```tsx
const queryDefaults = queryClient.getQueryDefaults(queryKey)
console.log(queryDefaults)
```

**选项**

- `queryKey: QueryKey`
  - 必须
  - 查询键

## `queryClient.setQueryDefaults`

`setQueryDefaults` 方法用于设置与指定查询键匹配的默认查询选项。

```tsx
queryClient.setQueryDefaults(queryKey, {
  staleTime: 1000 * 60 * 5, // 5 minutes
})
```

**选项**

- `queryKey: QueryKey`
  - 必须
  - 查询键
- `options: QueryDefaults`
  - 必须
  - 新的默认查询选项

## `queryClient.getMutationDefaults`

`getMutationDefaults` 方法用于获取与指定突变键匹配的默认突变选项。

```tsx
const mutationDefaults = queryClient.getMutationDefaults(mutationKey)
console.log(mutationDefaults)
```

**选项**

- `mutationKey: MutationKey`
  - 必须
  - 突变键

## `queryClient.setMutationDefaults`

`setMutationDefaults` 方法用于设置与指定突变键匹配的默认突变选项。

```tsx
queryClient.setMutationDefaults(mutationKey, {
  onMutate: variables => {
    // ...
  },
})
```

**选项**

- `mutationKey: MutationKey`
  - 必须
  - 突变键
- `options: MutationDefaults`
  - 必须
  - 新的默认突变选项

## `queryClient.getQueryCache`

`getQueryCache` 方法用于获取与查询客户端关联的查询缓存。

```tsx
const queryCache = queryClient.getQueryCache()
console.log(queryCache)
```

## `queryClient.getMutationCache`

`getMutationCache` 方法用于获取与查询客户端关联的突变缓存。

```tsx
const mutationCache = queryClient.getMutationCache()
console.log(mutationCache)
```

## `queryClient.clear`

`clear` 方法用于清除查询客户端中的所有缓存。

```tsx
queryClient.clear()
```

## `queryClient.resumePausedMutations`

`resumePausedMutations` 方法用于恢复在突变队列中暂停的突变。

```tsx
queryClient.resumePausedMutations()
```

