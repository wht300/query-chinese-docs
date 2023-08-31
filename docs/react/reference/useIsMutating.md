---
id: useIsMutating
title: useIsMutating
---

`useIsMutating` 是一个可选的钩子，它返回你的应用程序正在获取的突变（mutation）的数量（适用于应用程序范围的加载指示器）。

```tsx
import { useIsMutating } from '@tanstack/react-query'
// 有多少个突变正在获取？
const isMutating = useIsMutating()
// 有多少个与“posts”前缀匹配的突变正在获取？
const isMutatingPosts = useIsMutating({ mutationKey: ['posts'] })
```

**选项**

- `filters?: MutationFilters`：[突变过滤器](../guides/filters#mutation-filters)
- `context?: React.Context<QueryClient | undefined>`
  - 使用此选项使用自定义的 React Query 上下文。否则，将使用 `defaultContext`。

**返回值**

- `isMutating: number`
  - 将是你的应用程序当前正在获取的突变数量。
