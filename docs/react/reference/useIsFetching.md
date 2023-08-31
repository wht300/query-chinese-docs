---
id: useIsFetching
title: useIsFetching
---

`useIsFetching` 是一个可选的钩子，它返回你的应用程序正在加载或后台获取的查询的数量（适用于应用程序范围的加载指示器）。

```tsx
import { useIsFetching } from '@tanstack/react-query'
// 有多少个查询正在获取？
const isFetching = useIsFetching()
// 有多少个与“posts”前缀匹配的查询正在获取？
const isFetchingPosts = useIsFetching({ queryKey: ['posts'] })
```

**选项**

- `filters?: QueryFilters`：[查询过滤器](../guides/filters#query-filters)
- `context?: React.Context<QueryClient | undefined>`
  - 使用此选项使用自定义的 React Query 上下文。否则，将使用 `defaultContext`。

**返回值**

- `isFetching: number`
  - 将是你的应用程序当前正在加载或后台获取的查询数量。
