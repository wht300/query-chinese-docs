---
id: QueryCache
title: 查询缓存 (QueryCache)
---

`QueryCache` 是 TanStack Query 的存储机制。它存储了所有包含在其中的查询的数据、元信息和状态。

**通常情况下，您不会直接与 `QueryCache` 进行交互，而是使用特定缓存的 `QueryClient`。**

```tsx
import { QueryCache } from '@tanstack/react-query'

const queryCache = new QueryCache({
  onError: (error) => {
    console.log(error)
  },
  onSuccess: (data) => {
    console.log(data)
  },
  onSettled: (data, error) => {
    console.log(data, error)
  },
})

const query = queryCache.find(['posts'])
```

它的可用方法包括：

- [`find`](#querycachefind)
- [`findAll`](#querycachefindall)
- [`subscribe`](#querycachesubscribe)
- [`clear`](#querycacheclear)

**选项**

- `onError?: (error: unknown, query: Query) => void`
  - 可选
  - 当某个查询遇到错误时，将调用此函数。
- `onSuccess?: (data: unknown, query: Query) => void`
  - 可选
  - 当某个查询成功时，将调用此函数。
- `onSettled?:` (data: unknown | undefined, error: unknown | null, query: Query) => void
  - 可选
  - 当某个查询已完成（成功或错误）时，将调用此函数。

## 全局回调

`QueryCache` 上的 `onError`、`onSuccess` 和 `onSettled` 回调可用于在全局级别处理这些事件。它们与提供给 `QueryClient` 的 `defaultOptions` 不同，因为：

- `defaultOptions` 可以被每个查询覆盖 - 全局回调将**总是**被调用。
- `defaultOptions` 回调将对每个 Observer 被调用一次，而全局回调将仅对每个查询调用一次。

## `queryCache.find`

`find` 是一个稍微更高级的同步方法，用于从缓存中获取现有查询实例。此实例不仅包含查询的**所有**状态，还包括所有实例和查询的底层细节。如果查询不存在，将返回 `undefined`。

> 注意：对于大多数应用程序来说，通常不需要这样做，但在极少数情况下可能会需要更多关于查询的信息（例如，查看 query.state.dataUpdatedAt 时间戳以决定查询是否足够新，可以用作初始值）

```tsx
const query = queryCache.find(queryKey)
```

**选项**

- `queryKey?: QueryKey`：[查询键](../guides/query-keys)
- `filters?: QueryFilters`：[查询过滤器](../guides/filters#query-filters)

**返回值**

- `Query`
  - 缓存中的查询实例

## `queryCache.findAll`

`findAll` 是更高级的同步方法，用于从缓存中获取部分匹配查询键的现有查询实例。如果查询不存在，则返回空数组。

> 注意：对于大多数应用程序来说，通常不需要这样做，但在极少数情况下可能会需要更多关于查询的信息。

```tsx
const queries = queryCache.findAll(queryKey)
```

**选项**

- `queryKey?: QueryKey`：[查询键](../guides/query-keys)
- `filters?: QueryFilters`：[查询过滤器](../guides/filters#query-filters)

**返回值**

- `Query[]`
  - 缓存中的查询实例

## `queryCache.subscribe`

`subscribe` 方法可用于订阅整个查询缓存，并在缓存更新时获得有关查询状态更改或查询更新、添加或删除的安全/已知信息。

```tsx
const callback = (event) => {
  console.log(event.type, event.query)
}

const unsubscribe = queryCache.subscribe(callback)
```

**选项**

- `callback: (event: QueryCacheNotifyEvent) => void`
  - 每当通过其已跟踪的更新机制（例如，`query.setState`、`queryClient.removeQueries` 等）更新缓存时，将使用此函数调用查询缓存。不鼓励对缓存进行范围之外的突变，也不会触发订阅回调。

**返回值**

- `unsubscribe: Function => void`
  - 此函数将取消订阅回调对查询缓存的监听。

## `queryCache.clear`

`clear` 方法可用于清除整个缓存并重新开始。

```tsx
queryCache.clear()
```

[//]: # 'Materials'

## 进一步阅读

为了更好地理解 `QueryCache` 在内部的工作原理，请查看社区资源中的 [#18: Inside React Query](../community/tkdodos-blog#18-inside-react-query)。

[//]: # 'Materials'
