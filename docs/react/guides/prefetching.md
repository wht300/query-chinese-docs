---
id: prefetching
title: 预取数据
---

如果你足够幸运，你可能已经了解足够多关于用户行为的信息，以便在数据被需要之前就能够预取他们所需的数据！如果情况是这样的，你可以使用 `prefetchQuery` 方法来预取查询的结果并放入缓存中：

[//]: # 'Example'

```tsx
const prefetchTodos = async () => {
  // 这个查询的结果会像普通查询一样被缓存
  await queryClient.prefetchQuery({
    queryKey: ['todos'],
    queryFn: fetchTodos,
  })
}
```

[//]: # 'Example'

- 如果该查询的数据已经在缓存中且**没有失效**，数据将不会被获取
- 如果传递了 `staleTime`，例如 `prefetchQuery({queryKey: ['todos'], queryFn: fn, staleTime: 5000 })` 并且数据比指定的 `staleTime` 更旧，该查询将会被获取
- 如果在预取查询的情况下没有出现 `useQuery` 的实例，它将在 `cacheTime` 指定的时间后被删除和垃圾回收。

## 手动启动一个查询

另外，如果你已经同步地拥有了查询所需的数据，你不需要预取它。你可以直接使用[查询客户端的 `setQueryData` 方法](../reference/QueryClient#queryclientsetquerydata)通过键直接添加或更新查询的缓存结果。

[//]: # 'Example2'

```tsx
queryClient.setQueryData(['todos'], todos)
```

[//]: # 'Example2'

[//]: # 'Materials'

## 进一步阅读

如果你想深入了解如何在抓取数据之前将数据放入查询缓存中，请参阅社区资源中的[#17: 填充查询缓存](../community/tkdodos-blog#17-seeding-the-query-cache)。

[//]: # 'Materials'
