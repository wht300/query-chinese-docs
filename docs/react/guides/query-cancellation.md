---
id: query-cancellation
title: 查询取消
---

TanStack Query 为每个查询函数提供了一个[`AbortSignal` 实例](https://developer.mozilla.org/docs/Web/API/AbortSignal)，**如果在你的运行环境中可用**。当查询变得过时或不活动时，这个 `signal` 将会被中止。这意味着所有的查询都是可取消的，你可以根据需要在查询函数内部响应取消。最棒的部分在于，它允许你继续使用正常的异步/等待语法，同时获得自动取消的所有好处。

`AbortController` API 在[大多数运行环境](https://developer.mozilla.org/docs/Web/API/AbortController#browser_compatibility)中都是可用的，但如果运行环境不支持它，则查询函数将会接收到 `undefined`。如果希望的话，你可以选择对 `AbortController` API 进行 polyfill，有[几个可用的选项](https://www.npmjs.com/search?q=abortcontroller%20polyfill)。

## 默认行为

默认情况下，在其承诺被解析之前卸载或不再使用的查询不会被取消。这意味着在承诺解析后，结果数据将在缓存中可用。如果你已经开始接收一个查询，然后在完成之前卸载了组件。如果再次挂载组件，且查询还没有被垃圾回收，数据将会可用。

但是，如果你使用 `AbortSignal`，Promise 将会被取消（例如中止获取），因此查询也必须被取消。取消查询将导致其状态被 _恢复_ 到之前的状态。

## 使用 `fetch`

[//]: # 'Example'

```tsx
const query = useQuery({
  queryKey: ['todos'],
  queryFn: async ({ signal }) => {
    const todosResponse = await fetch('/todos', {
      // 将 signal 传递给一个 fetch
      signal,
    })
    const todos = await todosResponse.json()

    const todoDetails = todos.map(async ({ details }) => {
      const response = await fetch(details, {
        // 或将它传递给多个 fetch
        signal,
      })
      return response.json()
    })

    return Promise.all(todoDetails)
  },
})
```

[//]: # 'Example'

## 使用 `axios` [v0.22.0+](https://github.com/axios/axios/releases/tag/v0.22.0)

[//]: # 'Example2'

```tsx
import axios from 'axios'

const query = useQuery({
  queryKey: ['todos'],
  queryFn: ({ signal }) =>
    axios.get('/todos', {
      // 将 signal 传递给 `axios`
      signal,
    }),
})
```

[//]: # 'Example2'

### 使用版本低于 v0.22.0 的 `axios`

[//]: # 'Example3'

```tsx
import axios from 'axios'

const query = useQuery({
  queryKey: ['todos'],
  queryFn: ({ signal }) => {
    // 为此请求创建一个新的 CancelToken source
    const CancelToken = axios.CancelToken
    const source = CancelToken.source()

    const promise = axios.get('/todos', {
      // 将 source token 传递给你的请求
      cancelToken: source.token,
    })

    // 如果 TanStack Query 信号要求中止，取消请求
    signal?.addEventListener('abort', () => {
      source.cancel('Query was cancelled by TanStack Query')
    })

    return promise
  },
})
```

[//]: # 'Example3'

## 使用 `XMLHttpRequest`

[//]: # 'Example4'

```tsx
const query = useQuery({
  queryKey: ['todos'],
  queryFn: ({ signal }) => {
    return new Promise((resolve, reject) => {
      var oReq = new XMLHttpRequest()
      oReq.addEventListener('load', () => {
        resolve(JSON.parse(oReq.responseText))
      })
      signal?.addEventListener('abort', () => {
        oReq.abort()
        reject()
      })
      oReq.open('GET', '/todos')
      oReq.send()
    })
  },
})
```

[//]: # 'Example4'

## 使用 `graphql-request`

可以在客户端的 `request` 方法中设置 `AbortSignal`。

[//]: # 'Example5'

```tsx
const client = new GraphQLClient(endpoint)

const query = useQuery({
  queryKey: ['todos'],
  queryFn: ({ signal }) => {
    client.request({ document: query, signal })
  },
})
```

[//]: # 'Example5'

## 使用版本低于 v4.0.0 的 `graphql-request`

可以在 `GraphQLClient` 构造函数中设置 `AbortSignal`。

[//]: # 'Example6'

```tsx
const query = useQuery({
  queryKey: ['todos'],
  queryFn: ({ signal }) => {
    const client = new GraphQLClient(endpoint, {
      signal,
    })
    return client.request(query, variables)
  },
})
```

[//]: # 'Example6'

## 手动取消

你可能想要手动取消一个查询。例如，如果请求花费很长时间才能完成，你可以允许用户点击取消按钮来停止请求。要做到这一点，你只需要调用 `queryClient.cancelQueries({ queryKey })`，它会取消查询并将其恢复到之前的状态。如果已经使用了传递给查询函数的 `signal`，TanStack Query 还将取消 Promise。

[//]: # 'Example7'

```tsx
const query = useQuery({
  queryKey: ['todos'],
  queryFn: async ({ signal }) => {
    const resp = await fetch('/todos', { signal })
    return resp.json()
  },
})

const queryClient = useQueryClient()

return (
  <button
    onClick={(e) => {
      e.preventDefault()
      queryClient.cancelQueries({ queryKey: ['todos'] })
    }}
  >
    取消
  </button>
)
```

[//]: # 'Example7'
