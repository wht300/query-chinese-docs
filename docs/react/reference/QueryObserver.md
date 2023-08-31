---
id: QueryObserver
title: 查询观察器
---

## `QueryObserver`

`QueryObserver` 可用于观察和在查询之间进行切换。

```tsx
const observer = new QueryObserver(queryClient, { queryKey: ['posts'] })

const unsubscribe = observer.subscribe(result => {
  console.log(result)
  unsubscribe()
})
```

**选项**

`QueryObserver` 的选项与 [`useQuery`](../reference/useQuery) 的选项完全相同。
