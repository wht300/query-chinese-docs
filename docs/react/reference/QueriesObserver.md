---
id: QueriesObserver
title: 查询观察器 (QueriesObserver)
---

## `QueriesObserver`

`QueriesObserver` 可用于观察多个查询。

```tsx
const observer = new QueriesObserver(queryClient, [
  { queryKey: ['post', 1], queryFn: fetchPost },
  { queryKey: ['post', 2], queryFn: fetchPost },
])

const unsubscribe = observer.subscribe(result => {
  console.log(result)
  unsubscribe()
})
```

**选项**

`QueriesObserver` 的选项与 [`useQueries`](../reference/useQueries) 的选项完全相同。
