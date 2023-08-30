---
id: background-fetching-indicators
title: 后台获取指示器
---

对于查询的 `status === 'loading'` 状态足以显示查询的初始加载状态，但有时您可能还想显示一个额外的指示器，表示查询正在后台重新获取。为了实现这一点，查询还提供了一个 `isFetching` 布尔值，您可以使用它来显示它正在获取状态，无论 `status` 变量的状态如何：

[//]: # 'Example'

```tsx
function Todos() {
  const {
    status,
    data: todos,
    error,
    isFetching,
  } = useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodos,
  })

  return status === 'loading' ? (
    <span>Loading...</span>
  ) : status === 'error' ? (
    <span>Error: {error.message}</span>
  ) : (
    <>
      {isFetching ? <div>正在刷新...</div> : null}

      <div>
        {todos.map((todo) => (
          <Todo todo={todo} />
        ))}
      </div>
    </>
  )
}
```

[//]: # 'Example'

## 显示全局后台获取加载状态

除了单个查询的加载状态之外，如果您想在**任何**查询正在获取（包括在后台）时显示全局加载指示器，您可以使用 `useIsFetching` hook：

[//]: # 'Example2'

```tsx
import { useIsFetching } from '@tanstack/react-query'

function GlobalLoadingIndicator() {
  const isFetching = useIsFetching()

  return isFetching ? (
    <div>查询正在后台获取中...</div>
  ) : null
}
```

[//]: # 'Example2'
