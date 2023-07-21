---
id: optimistic-updates
title: 乐观更新 Optimistic Updates
ref: docs/react/guides/optimistic-updates.md
---

When you optimistically update your state before performing a mutation, there is a chance that the mutation will fail. In most of these failure cases, you can just trigger a refetch for your optimistic queries to revert them to their true server state. In some circumstances though, refetching may not work correctly and the mutation error could represent some type of server issue that won't make it possible to refetch. In this event, you can instead choose to rollback your update.

当在执行变更之前乐观地更新状态时，有可能变更会失败。在大多数情况下，你可以通过触发对乐观查询的重新获取来恢复它们到服务器的真实状态。但在某些情况下，重新获取可能无法正常工作，而且变更错误可能代表了一些无法重新获取的服务器问题。在这种情况下，您可以选择回滚您的更新。

To do this, `useMutation`'s `onMutate` handler option allows you to return a value that will later be passed to both `onError` and `onSettled` handlers as the last argument. In most cases, it is most useful to pass a rollback function.
为此，`useMutation`的`onMutate`处理程序选项允许您返回一个值，该值将作为最后一个参数传递给`onError`和`onSettled`处理程序。在大多数情况下，传递回滚函数是最有用的。

## 当添加新的待办事项时更新待办事项列表 - Updating a list of todos when adding a new todo

[//]: # 'Example'

```tsx
const queryClient = useQueryClient()

useMutation({
  mutationFn: updateTodo,
  // When mutate is called:
  // 当调用 mutate 时：
  onMutate: async (newTodo) => {
    // Cancel any outgoing refetches
    // (so they don't overwrite our optimistic update)
    // 取消所有正在进行的重新获取
    // （这样它们就不会覆盖我们的乐观更新）
    await queryClient.cancelQueries({ queryKey: ['todos'] })

    // Snapshot the previous value
    // 快照先前的值
    const previousTodos = queryClient.getQueryData(['todos'])

    // Optimistically update to the new value
    // 乐观地更新为新的值
    queryClient.setQueryData(['todos'], (old) => [...old, newTodo])

    // Return a context object with the snapshotted value
    // 返回一个带有快照值的上下文对象
    return { previousTodos }
  },
  // If the mutation fails,
  // use the context returned from onMutate to roll back
  // 如果变更失败，
  // 使用从 onMutate 返回的上下文回滚操作
  onError: (err, newTodo, context) => {
    queryClient.setQueryData(['todos'], context.previousTodos)
  },
  // Always refetch after error or success:
  // 总是在错误或成功后重新获取：
  onSettled: () => {
    queryClient.invalidateQueries({ queryKey: ['todos'] })
  },
})
```

[//]: # 'Example'

## Updating a single todo

[//]: # 'Example2'

```tsx
useMutation({
  mutationFn: updateTodo,
  // When mutate is called:
  onMutate: async (newTodo) => {
    // Cancel any outgoing refetches
    // (so they don't overwrite our optimistic update)
    await queryClient.cancelQueries({ queryKey: ['todos', newTodo.id] })

    // Snapshot the previous value
    const previousTodo = queryClient.getQueryData(['todos', newTodo.id])

    // Optimistically update to the new value
    queryClient.setQueryData(['todos', newTodo.id], newTodo)

    // Return a context with the previous and new todo
    return { previousTodo, newTodo }
  },
  // If the mutation fails, use the context we returned above
  onError: (err, newTodo, context) => {
    queryClient.setQueryData(
      ['todos', context.newTodo.id],
      context.previousTodo,
    )
  },
  // Always refetch after error or success:
  onSettled: (newTodo) => {
    queryClient.invalidateQueries({ queryKey: ['todos', newTodo.id] })
  },
})
```

[//]: # 'Example2'

You can also use the `onSettled` function in place of the separate `onError` and `onSuccess` handlers if you wish:

[//]: # 'Example3'

```tsx
useMutation({
  mutationFn: updateTodo,
  // ...
  onSettled: (newTodo, error, variables, context) => {
    if (error) {
      // do something
    }
  },
})
```

[//]: # 'Example3'
