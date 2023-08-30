---
id: InfiniteQueryObserver
title: 无限查询观察器 (InfiniteQueryObserver)
---

## `InfiniteQueryObserver`

`InfiniteQueryObserver` 可用于观察和在无限查询之间切换。

```tsx
const observer = new InfiniteQueryObserver(queryClient, {
  queryKey: ['posts'],
  queryFn: fetchPosts,
  getNextPageParam: (lastPage, allPages) => lastPage.nextCursor,
  getPreviousPageParam: (firstPage, allPages) => firstPage.prevCursor,
})

const unsubscribe = observer.subscribe(result => {
  console.log(result)
  unsubscribe()
})
```

**选项**

`InfiniteQueryObserver` 的选项与 [`useInfiniteQuery`](../reference/useInfiniteQuery) 完全相同。
