---
id: query-invalidation
title: 查询失效（Query Invalidation）
---

等待查询变得陈旧，然后再次获取它们并不总是有效，特别是当你确切知道查询的数据已过时，因为用户已经执行了某些操作。为此，`QueryClient` 提供了一个 `invalidateQueries` 方法，让你可以智能地标记查询为陈旧，并有可能重新获取它们！

[//]: # 'Example'

```tsx
// 使缓存中的所有查询变为陈旧状态
queryClient.invalidateQueries()
// 使所有以 `todos` 为开头的查询变为陈旧状态
queryClient.invalidateQueries({ queryKey: ['todos'] })
```

[//]: # 'Example'

> 注意：其他使用规范化缓存的库可能会尝试通过命令式方法或模式推断来更新本地查询的新数据，而 TanStack Query 则为您提供了工具，避免了维护规范化缓存所需的手动劳动，而是提供了**有针对性的失效、后台重新获取和最终的原子更新**。

当使用 `invalidateQueries` 使查询失效时，会发生两件事情：

- 它被标记为陈旧状态。此陈旧状态会覆盖在 `useQuery` 或相关钩子中使用的任何 `staleTime` 配置
- 如果查询当前通过 `useQuery` 或相关钩子呈现，它还将在后台被重新获取

## 使用 `invalidateQueries` 进行查询匹配

在使用像 `invalidateQueries` 和 `removeQueries`（以及其他支持部分查询匹配的方法）这样的 API 时，你可以通过前缀匹配多个查询，也可以非常具体地匹配一个确切的查询。有关您可以使用的筛选器类型的信息，请参阅 [查询筛选器](../guides/filters#query-filters)。

在此示例中，我们可以使用 `todos` 前缀来使任何以 `todos` 开头的查询失效：

[//]: # 'Example2'

```tsx
import { useQuery, useQueryClient } from '@tanstack/react-query'

// 从上下文中获取 QueryClient
const queryClient = useQueryClient()

queryClient.invalidateQueries({ queryKey: ['todos'] })

// 以下两个查询都将失效
const todoListQuery = useQuery({
queryKey: ['todos'],
queryFn: fetchTodoList,
})
const todoListQuery = useQuery({
queryKey: ['todos', { page: 1 }],
queryFn: fetchTodoList,
})
```

[//]: # 'Example2'

甚至可以通过将更具体的查询键传递给 `invalidateQueries` 方法，使具有特定变量的查询失效：

[//]: # 'Example3'

```tsx
queryClient.invalidateQueries({
queryKey: ['todos', { type: 'done' }],
})

// 以下查询将失效
const todoListQuery = useQuery({
queryKey: ['todos', { type: 'done' }],
queryFn: fetchTodoList,
})

// 但是，以下查询将不会失效
const todoListQuery = useQuery({
queryKey: ['todos'],
queryFn: fetchTodoList,
})
```

[//]: # 'Example3'

`invalidateQueries` API 非常灵活，因此，即使您只想失效不再具有任何其他变量或子键的 `todos` 查询，也可以向 `invalidateQueries` 方法传递 `exact: true` 选项：

[//]: # 'Example4'

```tsx
queryClient.invalidateQueries({
queryKey: ['todos'],
exact: true,
})

// 以下查询将失效
const todoListQuery = useQuery({
queryKey: ['todos'],
queryFn: fetchTodoList,
})

// 但是，以下查询将不会失效
const todoListQuery = useQuery({
queryKey: ['todos', { type: 'done' }],
queryFn: fetchTodoList,
})
```

[//]: # 'Example4'

如果您发现自己需要**更加**精细的控制，还可以向 `invalidateQueries` 方法传递一个谓词函数。此函数将接收查询缓存中的每个 `Query` 实例，并允许您返回是否要使该查询失效的 `true` 或 `false`：

[//]: # 'Example5'

```tsx
queryClient.invalidateQueries({
predicate: (query) =>
query.queryKey[0] === 'todos' && query.queryKey[1]?.version >= 10,
})

// 以下查询将失效
const todoListQuery = useQuery({
queryKey: ['todos', { version: 20 }],
queryFn: fetchTodoList,
})

// 以下查询将失效
const todoListQuery = useQuery({
queryKey: ['todos', { version: 10 }],
queryFn: fetchTodoList,
})

// 但是，以下查询将不会失效
const todoListQuery = useQuery({
queryKey: ['todos', { version: 5 }],
queryFn: fetchTodoList,
})
```

[//]: # 'Example5'
