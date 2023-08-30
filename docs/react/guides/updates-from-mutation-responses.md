---
id: updates-from-mutation-responses
title: 来自变更响应的更新
---

在处理**更新**服务器上的对象的变更时，通常在变更的响应中会自动返回新的对象。与其重新获取该项的任何查询并浪费网络调用用于我们已经拥有的数据，我们可以利用变更函数返回的对象，使用查询客户端的 [setQueryData](../reference/QueryClient#queryclientsetquerydata) 方法立即更新现有查询的新数据：

[//]: # 'Example'
```tsx
const queryClient = useQueryClient()

const mutation = useMutation({
  mutationFn: editTodo,
  onSuccess: data => {
    queryClient.setQueryData(['todo', { id: 5 }], data)
  }
})

mutation.mutate({
  id: 5,
  name: 'Do the laundry',
})

// 下面的查询将会使用成功变更的响应进行更新
const { status, data, error } = useQuery({
  queryKey: ['todo', { id: 5 }],
  queryFn: fetchTodoById,
})
```
[//]: # 'Example'

你可能想要将 `onSuccess` 逻辑绑定到可重用的变更中，为此，你可以创建一个像这样的自定义钩子：

[//]: # 'Example2'
```tsx
const useMutateTodo = () => {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: editTodo,
    // 注意第二个参数是 `mutate` 函数接收到的变量对象
    onSuccess: (data, variables) => {
      queryClient.setQueryData(['todo', { id: variables.id }], data)
    },
  })
}
```
[//]: # 'Example2'

## 不可变性

通过 `setQueryData` 进行的更新必须以 _immutable_ 的方式进行。**请勿**尝试直接通过就地突变数据（从缓存中检索的数据）来写入缓存。虽然一开始可能会起作用，但可能会导致难以察觉的错误。

[//]: # 'Example3'
```tsx
queryClient.setQueryData(
  ['posts', { id }],
  (oldData) => {
    if (oldData) {
      // ❌ 不要尝试这样做
      oldData.title = '我新的帖子标题'
    }
    return oldData
  })

queryClient.setQueryData(
  ['posts', { id }],
  // ✅ 这是正确的方式
  (oldData) => oldData ? {
    ...oldData,
    title: '我新的帖子标题'
  } : oldData
)
```
[//]: # 'Example3'
