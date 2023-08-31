---
id: useQueryClient
title: useQueryClient
---

`useQueryClient` 钩子返回当前的 `QueryClient` 实例。

```tsx
import { useQueryClient } from '@tanstack/react-query'

const queryClient = useQueryClient({ context })
```

**选项**

- `context?: React.Context<QueryClient | undefined>`
  - 使用此选项来使用自定义的 React Query 上下文。否则，将使用 `defaultContext`。
