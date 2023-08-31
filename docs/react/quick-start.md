---
id: quick-start
title: 快速入门

这段代码简要地说明了 React Query 的三个核心概念：

- [查询（Queries）](./guides/queries)
- [变异（Mutations）](./guides/mutations)
- [查询失效（Query Invalidation）](./guides/query-invalidation)

[//]: # 'Example'

如果您正在寻找一个完全功能的示例，请查看我们的[简单 CodeSandbox 示例](../examples/react/simple)

```tsx
import {
useQuery,
useMutation,
useQueryClient,
QueryClient,
QueryClientProvider,
} from '@tanstack/react-query'
import { getTodos, postTodo } from '../my-api'

// 创建一个客户端
const queryClient = new QueryClient()

function App() {
return (
// 将客户端提供给您的应用
<QueryClientProvider client={queryClient}>
<Todos />
</QueryClientProvider>
)
}

function Todos() {
// 访问客户端
const queryClient = useQueryClient()

// 查询
const query = useQuery({ queryKey: ['todos'], queryFn: getTodos })

// 变异
const mutation = useMutation({
mutationFn: postTodo,
onSuccess: () => {
// 失效并重新获取
queryClient.invalidateQueries({ queryKey: ['todos'] })
},
})

return (
<div>
<ul>
{query.data?.map((todo) => (
<li key={todo.id}>{todo.title}</li>
))}
</ul>

<button
onClick={() => {
mutation.mutate({
id: Date.now(),
title: 'Do Laundry',
})
}}
>
添加待办事项
</button>
</div>
)
}

render(<App />, document.getElementById('root'))
```

[//]: # 'Example'

这三个概念构成了 React Query 的大部分核心功能。本文档的后续部分将详细介绍这些核心概念中的每一个。
