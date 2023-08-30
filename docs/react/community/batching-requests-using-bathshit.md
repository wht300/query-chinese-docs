---
id: batching-requests
title: 批量请求
---

很多时候，我们希望将程序（或组件）发出的请求批量处理成一个单一的请求，以获取执行（渲染）程序所需的所有数据项。

假设你有一个组件 `<UserDetails userId={1}>`，它在内部获取用户数据并显示用户详情。但它可能会在页面上任意地使用，例如在用户列表中，还可能在包含一些消息和发送消息的用户详情的侧边栏中使用。

问题在于，协调所有已渲染用户详情组件的单一获取请求可能会很复杂，并且必须在父组件或状态管理模块中进行。

## 我们的目标

- 我们想定义一个组件，该组件通过用户 ID 获取并渲染用户详情。
- 我们想将所有已渲染组件在页面上的特定时间批量获取用户详情的请求。
- 如果在 `10 毫秒` 内渲染了一个或多个组件，我们将批量处理这些请求。
- 如果在 `10 毫秒` 窗口之后渲染了一个或多个组件，我们将批量处理这些后续请求。

## 实现

首先，让我们使用 [@yornaath/batshit](https://www.npmjs.com/package/@yornaath/batshit) 设置一个批处理器。

```ts
import { Batcher, windowScheduler, keyResolver } from "@yornaath/batshit"

type User = { id: number, name: string }

const users = Batcher<User, number>({
// fetcher 解析查询列表（这里只是一个用户 ID 列表）为一个单一的 API 调用。
fetcher: async (ids: number[]) => {
return api.users.where({
userId_in: ids
})
},
// 当我们调用 users.fetch 时，将使用字段 `id` 解析正确的用户。
resolver: keyResolver("id"),
// 这将在 10 毫秒内批处理所有对 users.fetch 的调用。
scheduler: windowScheduler(10)
})
```

然后，我们可以定义一个使用批处理器的查询钩子。如果在我们给定的 10 毫秒窗口内在应用程序的多个位置使用（渲染）了此钩子，所有这些请求都将被批量处理。当然，缓存和去重操作由 react-query 处理。

```ts
const useUser = (id: number) => {
return useQuery([ "users", id ], async () => {
return users.fetch(id)
})
}
```

> 注意：开发者使用体验与钩子单独获取单个请求的项目相同，可以在代码库中任意地使用。

最后，让我们定义一个使用我们制作的钩子的用户详情组件。

```ts
const UserDetails = (props: {userId: number}) => {
const {isLoading, data} = useUser(props.userId)
return <>
{
isLoading ?
<div>加载用户 {props.userId}</div>
:
<div>
用户：{data.name}
</div>
}
</>
}
```

### batshit 文档

有关如何创建具有自定义解析和请求调度的批处理器的更多信息，请参阅 batshit 仓库自述文件，
请前往 [yornaath/batshit github 仓库](https://github.com/yornaath/batshit/)。
