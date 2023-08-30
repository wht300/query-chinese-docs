---
title: React Query
---

`react-query` 包通过 React 提供了一个一流的 API，用于使用 TanStack Query。然而，你从这些 hooks 中获得的所有基本原语都是核心 API，它们在所有 TanStack 适配器中共享，包括 Query Client、查询结果、查询订阅等。

## 示例

```tsx
import { QueryClient, QueryClientProvider, useQuery } from '@tanstack/react-query'

const queryClient = new QueryClient()

function Example() {
  const query = useQuery({ queryKey: ['todos'], queryFn: fetchTodos })

  return (
    <div>
      {query.isLoading
        ? '加载中...'
        : query.isError
        ? '错误！'
        : query.data
        ? query.data.map((todo) => <div key={todo.id}>{todo.title}</div>)
        : null}
    </div>
  )
}

function App () {
  return (
    <QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
  )
}
```
