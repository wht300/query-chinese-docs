---
id: initial-query-data
title: 初始查询数据
---

在你需要之前，有许多方法可以为查询提供初始数据以填充缓存：

- 声明式：
  - 为查询提供`initialData`以在缓存为空时预填充它
- 命令式：
  - [使用`queryClient.prefetchQuery`预取数据](../guides/prefetching)
  - [使用`queryClient.setQueryData`手动将数据放入缓存](../guides/prefetching)

## 使用`initialData`预填充查询

在你的应用程序中可能会有一些时间，你已经拥有了查询所需的初始数据，可以直接提供给你的查询。如果是这种情况，你可以使用`config.initialData`选项来设置查询的初始数据，并跳过初始加载状态！

> 重要提示：`initialData`被持久保存到缓存中，因此不建议为此选项提供占位符、部分或不完整的数据，而应使用`placeholderData`

[//]: # '示例'

```tsx
const result = useQuery({
  queryKey: ['todos'],
  queryFn: () => fetch('/todos'),
  initialData: initialTodos,
})
```

[//]: # '示例'

### `staleTime`和`initialDataUpdatedAt`

默认情况下，`initialData`被视为完全新鲜，就像刚刚获取的一样。这也意味着它将影响到`staleTime`选项的解释。

- 如果你使用`initialData`配置查询观察器，并且没有`staleTime`（默认`staleTime: 0`），则查询在挂载时将立即重新获取：

  [//]: # '示例2'

  ```tsx
  // 将立即显示initialTodos，但在挂载后立即重新获取todos
  const result = useQuery({
    queryKey: ['todos'],
    queryFn: () => fetch('/todos'),
    initialData: initialTodos,
  })
  ```

  [//]: # '示例2'

- 如果你使用`initialData`配置查询观察器，并且`staleTime`为`1000`毫秒，则数据将被认为在同样的时间内是新鲜的，就像它刚刚从查询函数获取的一样。

  [//]: # '示例3'

  ```tsx
  // 将立即显示initialTodos，但在1000毫秒后遇到另一个交互事件之前不会重新获取
  const result = useQuery({
    queryKey: ['todos'],
    queryFn: () => fetch('/todos'),
    initialData: initialTodos,
    staleTime: 1000,
  })
  ```

  [//]: # '示例3'

- 那么如果你的`initialData`不是完全新鲜的呢？这就留下了最后一个配置，实际上是最准确的配置，它使用了一个称为`initialDataUpdatedAt`的选项。此选项允许你传递一个数值型JS时间戳（以毫秒为单位），表示initialData自上次更新以来的时间，例如`Date.now()`提供的内容。请注意，如果你有一个Unix时间戳，你需要将它乘以`1000`转换为JS时间戳。

  [//]: # '示例4'

  ```tsx
  // 将立即显示initialTodos，但在1000毫秒后遇到另一个交互事件之前不会重新获取
  const result = useQuery({
    queryKey: ['todos'],
    queryFn: () => fetch('/todos'),
    initialData: initialTodos,
    staleTime: 60 * 1000, // 1分钟
    // 这可以是10秒钟前或10分钟前
    initialDataUpdatedAt: initialTodosUpdatedTimestamp, // 例如 1608412420052
  })
  ```

  [//]: # '示例4'

  此选项允许staleTime用于其原始目的，确定数据需要多新鲜，同时也允许在initialData旧于staleTime的情况下在挂载时重新获取数据。在上面的示例中，我们的数据需要在1分钟内保持新鲜，我们可以向查询提供提示，initialData上次更新的时间，以便查询可以自行决定是否需要再次重新获取数据。

  > 如果你更愿意将数据视为**预取数据**，我们建议你使用`prefetchQuery`或`fetchQuery` API预先填充缓存，从而可以将`staleTime`与initialData独立配置

### 初始数据函数

如果访问查询的初始数据的过程很复杂，或者你不想在每次渲染时执行它，你可以将函数作为`initialData`的值传递。此函数仅在查询初始化时执行一次，节省宝贵的内存和/或CPU：

[//]: # '示例5'

```tsx
const result = useQuery({
  queryKey: ['todos'],
  queryFn: () => fetch('/todos'),
  initialData: () => getExpensiveTodos(),
})
```

[//]: # '示例5'

### 来自缓存的初始数据

在某些情况下，你可以从另一个查询的缓存结果中提供查询的初始数据。这种情况的一个很好的例子是从待办事项列表查询的缓存数据中搜索单个待办事项，然后将其作为个别待办事项查询的初始数据：

[//]: # '示例6'

```tsx
const result = useQuery({
  queryKey: ['todo', todoId],
  queryFn: () => fetch('/todos'),
  initialData: () => {
    // 使用'todos'查询中的待办事项作为此待办事项查询的初始数据
    return queryClient.getQueryData(['todos'])?.find((d) => d.id === todoId)
  },
})
```

[//]: # '示例6'

### 带有`initialDataUpdatedAt`的来自缓存的初始数据

从缓存中

获取初始数据意味着你用来查找初始数据的源查询可能是旧的，但是`initialData`不是。不建议使用人工的`staleTime`来阻止查询立即重新获取，而是建议将源查询的`dataUpdatedAt`传递给`initialDataUpdatedAt`。这为查询实例提供了决定是否以及何时重新获取查询的所有所需信息，而不考虑是否提供了初始数据。

[//]: # '示例7'

```tsx
const result = useQuery({
  queryKey: ['todos', todoId],
  queryFn: () => fetch(`/todos/${todoId}`),
  initialData: () =>
    queryClient.getQueryData(['todos'])?.find((d) => d.id === todoId),
  initialDataUpdatedAt: () =>
    queryClient.getQueryState(['todos'])?.dataUpdatedAt,
})
```

[//]: # '示例7'

### 来自缓存的条件初始数据

如果你用来查找初始数据的源查询是旧的，你可能不想使用缓存数据，只想从服务器获取数据。为了更轻松地做出这个决定，你可以使用`queryClient.getQueryState`方法，以获取有关源查询的更多信息，包括可以用于判断查询是否足够“新鲜”的`state.dataUpdatedAt`时间戳：

[//]: # '示例8'

```tsx
const result = useQuery({
  queryKey: ['todo', todoId],
  queryFn: () => fetch(`/todos/${todoId}`),
  initialData: () => {
    // 获取查询状态
    const state = queryClient.getQueryState(['todos'])

    // 如果查询存在，并且数据不早于10秒钟之前...
    if (state && Date.now() - state.dataUpdatedAt <= 10 * 1000) {
      // 返回个别待办事项
      return state.data.find((d) => d.id === todoId)
    }

    // 否则，返回undefined，让它从硬加载状态重新获取！
  },
})
```

[//]: # '示例8'
[//]: # 'Materials'

## 进一步阅读

要了解“初始数据”和“占位符数据”的比较，请查看[社区资源](../community/tkdodos-blog#9-placeholder-and-initial-data-in-react-query)。

[//]: # 'Materials'
