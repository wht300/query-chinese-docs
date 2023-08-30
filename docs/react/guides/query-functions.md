---
id: query-functions
title: 查询函数（Query Functions）
---

查询函数可以是任何**返回 Promise 的函数**。返回的 Promise 应该要么**解析数据**，要么**抛出错误**。

以下都是有效的查询函数配置：

[//]: # 'Example'

```tsx
useQuery({ queryKey: ['todos'], queryFn: fetchAllTodos })
useQuery({ queryKey: ['todos', todoId], queryFn: () => fetchTodoById(todoId) })
useQuery({
queryKey: ['todos', todoId],
queryFn: async () => {
const data = await fetchTodoById(todoId)
return data
},
})
useQuery({
queryKey: ['todos', todoId],
queryFn: ({ queryKey }) => fetchTodoById(queryKey[1]),
})
```

[//]: # 'Example'

## 处理和抛出错误

对于 TanStack Query 来说，要确定查询是否发生错误，查询函数**必须抛出**或返回**被拒绝的 Promise**。在查询函数中抛出的任何错误都将持久化到查询的`error`状态中。

[//]: # 'Example2'

```tsx
const { error } = useQuery({
queryKey: ['todos', todoId],
queryFn: async () => {
if (somethingGoesWrong) {
throw new Error('Oh no!')
}
if (somethingElseGoesWrong) {
return Promise.reject(new Error('Oh no!'))
}

return data
},
})
```

[//]: # 'Example2'

## 与 `fetch` 以及其他默认不抛出错误的客户端一起使用

尽管大多数像 `axios` 或 `graphql-request` 这样的工具会自动为不成功的 HTTP 调用抛出错误，但某些工具（例如 `fetch`）默认不会抛出错误。如果是这种情况，你需要自己抛出错误。以下是在常用的 `fetch` API 中实现这一点的简单方法：

[//]: # 'Example3'

```tsx
useQuery({
queryKey: ['todos', todoId],
queryFn: async () => {
const response = await fetch('/todos/' + todoId)
if (!response.ok) {
throw new Error('Network response was not ok')
}
return response.json()
},
})
```

[//]: # 'Example3'

## 查询函数变量

查询键不仅用于唯一标识你要获取的数据，而且还作为 QueryFunctionContext 的一部分方便地传递到查询函数中。虽然不总是必需的，但这使得在需要时可以提取查询函数：

[//]: # 'Example4'

```tsx
function Todos({ status, page }) {
const result = useQuery({
queryKey: ['todos', { status, page }],
queryFn: fetchTodoList,
})
}

// 在查询函数中访问键、status 和 page 变量！
function fetchTodoList({ queryKey }) {
const [_key, { status, page }] = queryKey
return new Promise()
}
```

[//]: # 'Example4'

### QueryFunctionContext

`QueryFunctionContext` 是传递给每个查询函数的对象，包含以下内容：

- `queryKey: QueryKey`：[查询键](../guides/query-keys)
- `pageParam?: unknown`
- 仅适用于[无限查询](../guides/infinite-queries)
- 用于获取当前页的页面参数
- `signal?: AbortSignal`
- 由 TanStack Query 提供的 [AbortSignal](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal) 实例
- 可用于[查询取消](../guides/query-cancellation)
- `meta: Record<string, unknown> | undefined`
- 一个可选字段，你可以用它填充有关查询的其他信息
