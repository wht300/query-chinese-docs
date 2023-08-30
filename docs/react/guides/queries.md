---
id: queries
title: 查询（Queries）

## 查询基础

查询是对异步数据源的声明性依赖，与一个**唯一键（unique key）**相关联。查询可以与任何基于 Promise 的方法（包括 GET 和 POST 方法）一起使用，以从服务器获取数据。如果你的方法在服务器上修改数据，我们建议使用[变更（Mutations）](../guides/mutations)。

要在你的组件或自定义钩子中订阅一个查询，至少调用 `useQuery` 钩子时需要：

- 一个**查询的唯一键**
- 一个返回 Promise 的函数，该函数：
- 解析数据，或者
- 抛出一个错误

[//]: # 'Example'

```tsx
import { useQuery } from '@tanstack/react-query'

function App() {
const info = useQuery({ queryKey: ['todos'], queryFn: fetchTodoList })
}
```

[//]: # 'Example'

你提供的**唯一键**在内部用于重新获取、缓存和在整个应用程序中共享你的查询。

`useQuery` 返回的查询结果包含了关于查询的所有信息，你可以用来进行模板化和其他数据使用：

[//]: # 'Example2'

```tsx
const result = useQuery({ queryKey: ['todos'], queryFn: fetchTodoList })
```

[//]: # 'Example2'

`result` 对象包含一些非常重要的状态，你需要注意以提高生产效率。一个查询在任何给定的时刻只能处于以下状态之一：

- `isLoading` 或 `status === 'loading'` - 查询尚无数据
- `isError` 或 `status === 'error'` - 查询遇到错误
- `isSuccess` 或 `status === 'success'` - 查询成功，数据可用

除了这些主要状态，根据查询的状态，还有更多的信息可用：

- `error` - 如果查询处于 `isError` 状态，错误可以通过 `error` 属性获得。
- `data` - 如果查询处于 `isSuccess` 状态，数据可以通过 `data` 属性获得。

对于**大多数**查询来说，通常只需要检查 `isLoading` 状态，然后是 `isError` 状态，最后假设数据可用并渲染成功的状态：

[//]: # 'Example3'

```tsx
function Todos() {
const { isLoading, isError, data, error } = useQuery({
queryKey: ['todos'],
queryFn: fetchTodoList,
})

if (isLoading) {
return <span>Loading...</span>
}

if (isError) {
return <span>Error: {error.message}</span>
}

// 到这一步，我们可以假设 `isSuccess === true`
return (
<ul>
{data.map((todo) => (
<li key={todo.id}>{todo.title}</li>
))}
</ul>
)
}
```

[//]: # 'Example3'

如果布尔值不适合你，你也可以始终使用 `status` 状态：

[//]: # 'Example4'

```tsx
function Todos() {
const { status, data, error } = useQuery({
queryKey: ['todos'],
queryFn: fetchTodoList,
})

if (status === 'loading') {
return <span>Loading...</span>
}

if (status === 'error') {
return <span>Error: {error.message}</span>
}

// status === 'success' 也是成立的，但是 "else" 逻辑同样适用
return (
<ul>
{data.map((todo) => (
<li key={todo.id}>{todo.title}</li>
))}
</ul>
)
}
```

[//]: # 'Example4'

如果在访问数据之前已经检查了 `loading` 和 `error`，TypeScript 也会正确缩小 `data` 的类型。

### FetchStatus

除了 `status` 字段，`result` 对象还会附带额外的 `fetchStatus` 属性，有以下选项：

- `fetchStatus === 'fetching'` - 查询当前正在获取数据。
- `fetchStatus === 'paused'` - 查询想要获取数据，但已被暂停。在[网络模式](../guides/network-mode)指南中详细了解。
- `fetchStatus === 'idle'` - 查询当前没有任何操作。

### 为什么有两种不同的状态？

后台重新获取和过期时同时重新验证的逻辑使得 `status` 和 `fetchStatus` 的所有组合都是可能的。例如：

- 处于 `success` 状态的查询通常会处于 `idle` 的 `fetchStatus`，但如果正在进行后台重新获取，它也可能处于 `fetching` 状态。
- 如果查询挂载并且没有数据，通常会处于 `loading` 状态和 `fetching` 的 `fetchStatus`，但如果没有网络连接，它也可能是 `paused`。

因此，请记住，查询可以处于 `loading` 状态而实际上并不获取数据。作为一个经验法则：

- `status` 提供有关 `data` 的信息：我们是否有任何数据？
- `fetchStatus` 提供有关 `queryFn` 的信息：它是否正在运行？

[//]: # 'Materials'

## 进一步阅读

如果你想了解一个执行状态检查的替代方法，请查看[社区资源](../community/tkdodos-blog#4-status-checks-in-react-query)。

[//]: # 'Materials'
