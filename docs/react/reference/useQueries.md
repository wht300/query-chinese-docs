---
id: useQueries
title: useQueries 使用方法
---

`useQueries` 钩子可用于获取多个不定数量的查询：

```tsx
const results = useQueries({
  queries: [
    { queryKey: ['post', 1], queryFn: fetchPost, staleTime: Infinity },
    { queryKey: ['post', 2], queryFn: fetchPost, staleTime: Infinity }
  ]
})
```

**选项**

`useQueries` 钩子接受一个选项对象，其中包含一个 **queries** 键，其值是一个数组，数组中的每个元素都是与 [`useQuery` 钩子](../reference/useQuery) 相同的查询选项对象（不包括 `context` 选项）。

- `context?: React.Context<QueryClient | undefined>`
  - 可以使用此选项来使用自定义的 React Query 上下文。否则，将使用 `defaultContext`。

> 在查询对象数组中多次使用相同的查询键可能会导致某些数据在查询之间共享，例如在使用 `placeholderData` 和 `select` 时。为避免这种情况，考虑对查询进行去重，并将结果映射回所需的结构。

**返回结果**

`useQueries` 钩子返回一个包含所有查询结果的数组。返回的顺序与输入顺序相同。
