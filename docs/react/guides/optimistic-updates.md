---
id: optimistic-updates
title: 乐观更新
---

当您在执行突变之前进行乐观状态更新时，有可能突变会失败。在大多数这些失败情况下，您可以只需触发乐观查询的重新提取，将其恢复为其真实的服务器状态。但在某些情况下，重新提取可能无法正常工作，突变错误可能代表一些无法重新提取的服务器问题。在这种情况下，您可以选择回滚您的更新。

要做到这一点，`useMutation`的`onMutate`处理程序选项允许您返回一个值，稍后将作为最后一个参数传递给`onError`和`onSettled`处理程序。在大多数情况下，最有用的是传递一个回滚函数。

## 添加新任务时更新任务列表

[//]: # 'Example'

```tsx
const queryClient = useQueryClient();

useMutation({
  mutationFn: updateTodo,
  // 当调用 mutate 时：
  onMutate: async (newTodo) => {
    // 取消任何正在进行的重新提取
    // （以便它们不会覆盖我们的乐观更新）
    await queryClient.cancelQueries({ queryKey: ['todos'] });

    // 快照先前的值
    const previousTodos = queryClient.getQueryData(['todos']);

    // 乐观地更新为新值
    queryClient.setQueryData(['todos'], (old) => [...old, newTodo]);

    // 返回一个带有快照值的上下文对象
    return { previousTodos };
  },
  // 如果突变失败，
  // 使用从 onMutate 返回的上下文回滚
  onError: (err, newTodo, context) => {
    queryClient.setQueryData(['todos'], context.previousTodos);
  },
  // 无论出现错误还是成功，总是重新提取：
  onSettled: () => {
    queryClient.invalidateQueries({ queryKey: ['todos'] });
  },
});


```

[//]: # 'Example'

## 更新单个任务

[//]: # 'Example2'

```tsx
useMutation({
  mutationFn: updateTodo,
  // 当调用 mutate 时：
  onMutate: async (newTodo) => {
    // 取消任何正在进行的重新提取
    // （以便它们不会覆盖我们的乐观更新）
    await queryClient.cancelQueries({ queryKey: ['todos', newTodo.id] });

    // 快照先前的值
    const previousTodo = queryClient.getQueryData(['todos', newTodo.id]);

    // 乐观地更新为新值
    queryClient.setQueryData(['todos', newTodo.id], newTodo);

    // 返回带有先前和新任务的上下文
    return { previousTodo, newTodo };
  },
  // 如果突变失败，使用我们上面返回的上下文
  onError: (err, newTodo, context) => {
    queryClient.setQueryData(
      ['todos', context.newTodo.id],
      context.previousTodo
    );
  },
  // 无论出现错误还是成功，总是重新提取：
  onSettled: (newTodo) => {
    queryClient.invalidateQueries({ queryKey: ['todos', newTodo.id] });
  },
});

```

[//]: # 'Example2'

您还可以在需要时使用 `onSettled` 函数代替单独的 `onError` 和 `onSuccess` 处理程序：

[//]: # 'Example3'

```tsx
useMutation({
  mutationFn: updateTodo,
  // ...
  onSettled: (newTodo, error, variables, context) => {
    if (error) {
      // 做些什么
    }
  },
});

```

[//]: # 'Example3'
