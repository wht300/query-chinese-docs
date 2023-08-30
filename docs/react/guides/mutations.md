---
id: mutations
title: 变更
---

不同于查询，变更通常用于创建/更新/删除数据或执行服务器端效果。为此，TanStack Query 导出了 `useMutation` 钩子。

以下是向服务器添加新待办事项的变更示例：

```tsx
function App() {
  const mutation = useMutation({
    mutationFn: (newTodo) => {
      return axios.post('/todos', newTodo)
    },
  })

  return (
    <div>
      {mutation.isLoading ? (
        '正在添加待办事项...'
      ) : (
        <>
          {mutation.isError ? (
            <div>发生错误：{mutation.error.message}</div>
          ) : null}

          {mutation.isSuccess ? <div>待办事项已添加！</div> : null}

          <button
            onClick={() => {
              mutation.mutate({ id: new Date(), title: '做洗衣' })
            }}
          >
            创建待办事项
          </button>
        </>
      )}
    </div>
  )
}
```

变更在任何给定时刻只能处于以下状态之一：

- `isIdle` 或 `status === 'idle'` - 变更当前处于空闲或新的/重置状态
- `isLoading` 或 `status === 'loading'` - 变更正在运行
- `isError` 或 `status === 'error'` - 变更遇到错误
- `isSuccess` 或 `status === 'success'` - 变更成功且变更数据可用

除了这些主要状态外，根据变更的状态，还可以获得更多信息：

- `error` - 如果变更处于 `error` 状态，错误将通过 `error` 属性可用。
- `data` - 如果变更处于 `success` 状态，数据将通过 `data` 属性可用。

在上面的示例中，您还看到可以通过使用**单个变量或对象**调用 `mutate` 函数将变量传递给变更函数。

即使只是使用变量，变更也并不是特别特殊，但是当与 `onSuccess` 选项一起使用时，[查询客户端的 `invalidateQueries` 方法](../reference/QueryClient#queryclientinvalidatequeries) 和 [查询客户端的 `setQueryData` 方法](../reference/QueryClient#queryclientsetquerydata) 使变更成为一个非常强大的工具。

> 重要提示：`mutate` 函数是一个异步函数，这意味着您不能在 **React 16 及之前** 的事件回调中直接使用它。如果您需要在 `onSubmit` 中访问事件，您需要将 `mutate` 包装在另一个函数中。这是由于[React 事件池](https://reactjs.org/docs/legacy-event-pooling.html)。

```tsx
// 这在 React 16 及之前不起作用
const CreateTodo = () => {
  const mutation = useMutation({
    mutationFn: (event) => {
      event.preventDefault()
      return fetch('/api', new FormData(event.target))
    },
  })

  return <form onSubmit={mutation.mutate}>...</form>
}

// 这会起作用
const CreateTodo = () => {
  const mutation = useMutation({
    mutationFn: (formData) => {
      return fetch('/api', formData)
    },
  })
  const onSubmit = (event) => {
    event.preventDefault()
    mutation.mutate(new FormData(event.target))
  }

  return <form onSubmit={onSubmit}>...</form>
}
```

## 重置变更状态

有时您需要清除变更请求的 `error` 或 `data`。为此，您可以使用 `reset` 函数处理：

```tsx
const CreateTodo = () => {
  const [title, setTitle] = useState('')
  const mutation = useMutation({ mutationFn: createTodo })

  const onCreateTodo = (e) => {
    e.preventDefault()
    mutation.mutate({ title })
  }

  return (
    <form onSubmit={onCreateTodo}>
      {mutation.error && (
        <h5 onClick={() => mutation.reset()}>{mutation.error}</h5>
      )}
      <input
        type="text"
        value={title}
        onChange={(e) => setTitle(e.target.value)}
      />
      <br />
      <button type="submit">创建待办事项</button>
    </form>
  )
}
```

## 变更副作用

`useMutation` 配备了一些助手选项，允许在变更生命周期的任何阶段快速、轻松地执行副作用。这些对于[在变更后使查询无效和重新获取](../guides/invalidations-from-mutations)以及甚至[乐观更新](../guides/optimistic-updates)非常有用。

```tsx
useMutation({
  mutationFn: addTodo,
  onMutate: (variables) => {
    // 即将发生变更！

    // 可选地返回一个包含数据的上下文，以在需要时回滚
    return { id: 1 }
  },
  onError: (error, variables, context) => {
    // 发生错误！
    console.log(`正在回滚带有 ID ${context.id} 的乐观更新`)
  },
  onSuccess: (data, variables, context) => {
    // 发生了变更！
  },
  onSettled: (data, error, variables, context) => {
    // 无论错误还是成功... 都无所谓！
  },
})
```

当在任何回调函数中返回一个 promise 时，它会在下一个回调被调用之前被等待：

```tsx
useMutation({
  mutationFn: addTodo,
  onSuccess: async () => {
    console.log("我先执行！")
  },
  onSettled: async () => {
    console.log("我第二个执行！")
  },
})
```

您可能会发现，除了在 `useMutation` 上定义的处理程序之外，您可能还希望在调用 `mutate` 后触发其他回调。这可用于触发特定于组件的副作用。为此，您可以在变更变量之后的 `mutate` 函数中提供任何相同的回调选项。支持的选项包括：`onSuccess`、`onError` 和 `onSettled`。请注意，这

些附加回调在变更完成之前，如果您的组件在变更完成之前卸载，它们将不会运行。

```tsx
useMutation({
  mutationFn: addTodo,
  onSuccess: (data, variables, context) => {
    // 我会第一个触发
  },
  onError: (error, variables, context) => {
    // 我会第一个触发
  },
  onSettled: (data, error, variables, context) => {
    // 我会第一个触发
  },
})

mutate(todo, {
  onSuccess: (data, variables, context) => {
    // 我会第二个触发！
  },
  onError: (error, variables, context) => {
    // 我会第二个触发！
  },
  onSettled: (data, error, variables, context) => {
    // 我会第二个触发！
  },
})
```

### 连续变更

在处理连续变更时，处理 `onSuccess`、`onError` 和 `onSettled` 回调时有些差异。当传递给 `mutate` 函数时，它们将仅被触发一次，仅当组件仍然挂载时。这是因为每次调用 `mutate` 函数时，变更观察器都会被删除和重新订阅。相反，在每次调用 `mutate` 时，`useMutation` 处理程序会执行。

> 请注意，很可能传递给 `useMutation` 的 `mutationFn` 是异步的。在这种情况下，变更完成的顺序可能与 `mutate` 函数调用的顺序不同。

```tsx
useMutation({
  mutationFn: addTodo,
  onSuccess: (data, error, variables, context) => {
    // 将调用 3 次
  },
})

[('待办事项 1', '待办事项 2', '待办事项 3')].forEach((todo) => {
  mutate(todo, {
    onSuccess: (data, error, variables, context) => {
      // 仅会执行一次，对于最后一个变更（待办事项 3），无论哪个变更首先解析
    },
  })
})
```

## Promises

使用 `mutateAsync` 代替 `mutate`，以获取在成功时解析的 promise，或在错误时抛出的 promise。例如，这可以用来组合副作用。

```tsx
const mutation = useMutation({ mutationFn: addTodo })

try {
  const todo = await mutation.mutateAsync(todo)
  console.log(todo)
} catch (error) {
  console.error(error)
} finally {
  console.log('完成')
}
```

## 重试

默认情况下，TanStack Query 不会在错误时重试变更，但通过 `retry` 选项可以实现重试：

```tsx
const mutation = useMutation({
  mutationFn: addTodo,
  retry: 3,
})
```

如果变更由于设备离线而失败，在设备重新连接时，它们将按照相同的顺序进行重试。

## 持久化变更

如果需要，变更可以持久化到存储中，并在以后的某个时间点恢复。这可以使用 hydration 函数来实现：

```tsx
const queryClient = new QueryClient()

// 定义 "addTodo" 变更
queryClient.setMutationDefaults(['addTodo'], {
  mutationFn: addTodo,
  onMutate: async (variables) => {
    // 取消当前待办事项列表的查询
    await queryClient.cancelQueries({ queryKey: ['todos'] })

    // 创建乐观的待办事项
    const optimisticTodo = { id: uuid(), title: variables.title }

    // 将乐观的待办事项添加到待办事项列表
    queryClient.setQueryData(['todos'], (old) => [...old, optimisticTodo])

    // 返回带有乐观的待办事项的上下文
    return { optimisticTodo }
  },
  onSuccess: (result, variables, context) => {
    // 用结果替换待办事项列表中的乐观待办事项
    queryClient.setQueryData(['todos'], (old) =>
      old.map((todo) =>
        todo.id === context.optimisticTodo.id ? result : todo,
      ),
    )
  },
  onError: (error, variables, context) => {
    // 从待办事项列表中删除乐观待办事项
    queryClient.setQueryData(['todos'], (old) =>
      old.filter((todo) => todo.id !== context.optimisticTodo.id),
    )
  },
  retry: 3,
})

// 在某个组件中启动变更：
const mutation = useMutation({ mutationKey: ['addTodo'] })
mutation.mutate({ title: '标题' })

// 如果变更因为设备离线等原因而暂停，那么在应用程序退出时可以将暂停的变更脱水化：
const state = dehydrate(queryClient)

// 然后，在应用程序启动时可以再次将变更水化：
hydrate(queryClient, state)

// 恢复暂停的变更：
queryClient.resumePausedMutations()
```

### 持久化离线变更

如果您使用 [persistQueryClient 插件](../plugins/persistQueryClient) 将离线变更持久化到存储中，除非您提供默认的变更函数，否则在页面重新加载时将无法恢复变更。

这是技术限制。当将状态持久化到外部存储时，只持久化变更的状态，因为函数无法序列化。在水合化后，触发变更的组件可能未挂载，因此调用 `resumePausedMutations` 可能会引发错误：“找不到 mutationFn”。

```tsx
const persister = createSyncStoragePersister({
  storage: window.localStorage,
})
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      cacheTime: 1000 * 60 * 60 * 24, // 24 小时
    },
  },
})

// 我们需要一个默认的变更函数，以便在

页面重新加载后，暂停的变更可以恢复
queryClient.setMutationDefaults(['todos'], {
  mutationFn: ({ id, data }) => {
    return api.updateTodo(id, data)
  },
})

export default function App() {
  return (
    <PersistQueryClientProvider
      client={queryClient}
      persistOptions={{ persister }}
      onSuccess={() => {
        // 在初始从 localStorage 恢复成功后，恢复变更
        queryClient.resumePausedMutations()
      }}
    >
      <RestOfTheApp />
    </PersistQueryClientProvider>
  )
}
```


[//]: # 'Example11'

我们还有一个广泛的[离线示例](../examples/react/offline)，涵盖了查询和变更。

[//]: # 'Materials'

## Further reading

关于变更的更多信息，请查看社区资源中的[#12：精通 React Query 中的变更](../community/tkdodos-blog#12-mastering-mutations-in-react-query)。


[//]: # 'Materials'
