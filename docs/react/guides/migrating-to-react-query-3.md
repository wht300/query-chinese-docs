---
id: migrating-to-react-query-3
title: 迁移到 React Query 3
---

之前的 React Query 版本带来了一些令人惊叹的新功能、更多的魔力和整体更好的体验，为该库带来了大规模的采用，并且也为该库带来了大量的优化（问题/贡献），并且揭示了一些需要更多改进的地方，以使库变得更好。v3 版本包含了这种完善。

## 概述

- 更可扩展和可测试的缓存配置
- 更好的 SSR 支持
- 在任何地方使用数据滞后（之前是 usePaginatedQuery）！
- 双向无限查询
- 查询数据选择器！
- 在使用之前完全配置查询和/或变更的默认值
- 更细粒度的可选渲染优化
- 新的 `useQueries` 钩子！（可变长度的并行查询执行）
- `useIsFetching()` 钩子的查询过滤支持！
- 变更的重试/离线/重放支持
- 在 React 之外观察查询/变更
- 在任何您想要的地方使用 React Query 核心逻辑！
- 通过 `react-query/devtools` 打包/放置的 Devtools
- 缓存持久性到 web 存储（通过 `react-query/persistQueryClient-experimental` 和 `react-query/createWebStoragePersistor-experimental` 进行实验性支持）

## 破坏性变更

### `QueryCache` 已拆分为 `QueryClient` 和较低级别的 `QueryCache` 和 `MutationCache` 实例。

`QueryCache` 包含所有查询，`MutationCache` 包含所有变更，`QueryClient` 可用于设置配置并与它们进行交互。

这带来了一些好处：

- 允许不同类型的缓存。
- 具有不同配置的多个客户端可以使用相同的缓存。
- 客户端可用于跟踪查询，可以在 SSR 上用于共享缓存。
- 客户端 API 更侧重于一般用途。
- 更容易测试各个组件。

在创建 `new QueryClient()` 时，如果您不提供它们，将为您自动创建 `QueryCache` 和 `MutationCache`。

```tsx
import { QueryClient } from 'react-query'

const queryClient = new QueryClient()
```

### `ReactQueryConfigProvider` 和 `ReactQueryCacheProvider` 已分别由 `QueryClientProvider` 取代

现在可以在 `QueryClient` 中指定查询和变更的默认选项：

**请注意，现在是 `defaultOptions`，而不是 `defaultConfig`**

```tsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      // 查询选项
    },
    mutations: {
      // 变更选项
    },
  },
})
```

`QueryClientProvider` 组件现在用于将 `QueryClient` 连接到应用程序：

```tsx
import { QueryClient, QueryClientProvider } from 'react-query'

const queryClient = new QueryClient()

function App() {
  return <QueryClientProvider client={queryClient}>...</QueryClientProvider>
}
```

### 默认的 `QueryCache` 已经消失。**这次是真的！**

正如之前已经注意到的，已经不再有默认的 `QueryCache` 在主包中创建或导出。**您必须通过 `new QueryClient()` 或 `new QueryCache()` 创建自己的缓存（然后可以将其传递给 `new QueryClient({ queryCache })`）**

### 已删除不推荐使用的 `makeQueryCache` 实用程序。

虽然已经有很长一段时间了，但它终于消失了 :)

### `QueryCache.prefetchQuery()` 已移动到 `QueryClient.prefetchQuery()`

新的 `QueryClient.prefetchQuery()` 函数是异步的，但**不会返回查询中的数据**。如果需要数据，使用新的 `QueryClient.fetchQuery()` 函数

```tsx
// 预取查询：
await queryClient.prefetchQuery('posts', fetchPosts)

// 获取查询：
try {
  const data = await queryClient.fetchQuery('posts', fetchPosts)
} catch (error) {
  // 错误处理
}
```

### `ReactQueryErrorResetBoundary` 和 `QueryCache.resetErrorBoundaries()` 已被 `QueryErrorResetBoundary` 和 `useQueryErrorResetBoundary()` 取代。

这两者共同提供与以前相同的体验，但增加了选择要重置的组件树的控制。更多信息，请参见：

- [QueryErrorResetBoundary](../reference/QueryErrorResetBoundary)
- [useQueryErrorResetBoundary](../reference/useQueryErrorResetBoundary)

### `QueryCache.getQuery()` 已被 `QueryCache.find()` 取代。

现在应该使用 `QueryCache.find()` 来从缓存中查找单个查询

### `QueryCache.getQueries()` 已被移动到 `QueryCache.findAll()`。

现在应该使用 `QueryCache.findAll()` 来从缓存中查找多个查询

### `QueryCache.isFetching` 已被移动到 `QueryClient.isFetching()`。

**请注意，现在它是一个函数，而不是一个属性**

### `useQueryCache` 钩子已被 `useQueryClient` 钩子取代。

它将为其组件树返回提供的 `queryClient`，除了改名之外，不应该需要进行太多调整。

### 查询键的部分/片段不再自动扩展到查询函数。

现在，内联函数是将参数传递给查询函数的建议方式：

```tsx
// 旧的
useQuery(['post', id], (_key, id) => fetchPost(id))

// 新的
useQuery(['post', id], () => fetchPost(id))
```

如果您仍然坚持不使用内联函数，您可以使用新传递的 `QueryFunctionContext`：

```tsx
useQuery(['post', id], context => fetchPost(context.queryKey[1]))
```

### 无限查询页面

参数现在通过 `QueryFunctionContext.pageParam` 传递

它们先前是添加为查询函数中的最后一个查询键参数，但对于某些模式来说，这被证明是困难的

```tsx
// 旧的
useInfiniteQuery(['posts'], (_key, pageParam = 0) => fetchPosts(pageParam))

// 新的
useInfiniteQuery(['posts'], ({ pageParam = 0 }) => fetchPosts(pageParam))
```

### `usePaginatedQuery()` 已被移除，以支持 `keepPreviousData` 选项

新的 `keepPreviousData` 选项适用于 `useQuery` 和 `useInfiniteQuery`，并且对数据具有相同的 "滞后" 效果：

```tsx
import { useQuery } from 'react-query'

function Page({ page }) {
  const { data } = useQuery(['page', page], fetchPage, {
    keepPreviousData: true,
  })
}
```

### `useInfiniteQuery()` 现在是双向的

`useInfiniteQuery()` 接口已更改，以完全支持双向无限列表。

- `options.getFetchMore` 已重命名为 `options.getNextPageParam`
- `queryResult.canFetchMore` 已重命名为 `queryResult.hasNextPage`
- `queryResult.fetchMore` 已重命名为 `queryResult.fetchNextPage`
- `queryResult.isFetchingMore` 已重命名为 `queryResult.isFetchingNextPage`
- 新增了 `options.getPreviousPageParam` 选项
- 新增了 `queryResult.hasPreviousPage` 属性
- 新增了 `queryResult.fetchPreviousPage` 属性
- 新增了 `queryResult.isFetchingPreviousPage`
- 无限查询的 `data` 现在是一个包含 `pages` 和用于获取页面的 `pageParams` 的对象：`{ pages: [data, data, data], pageParams: [...]}`

一个方向：

```tsx
const {
  data,
  fetchNextPage,
  hasNextPage,
  isFetchingNextPage,
} = useInfiniteQuery(
  'projects',
  ({ pageParam = 0 }) => fetchProjects(pageParam),
  {
    getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
  }
)
```

两个方向：

```tsx
const {
  data,
  fetchNextPage,
  fetchPreviousPage,
  hasNextPage,
  hasPreviousPage,
  isFetchingNextPage,
  isFetchingPreviousPage,
} = useInfiniteQuery(
  'projects',
  ({ pageParam = 0 }) => fetchProjects(pageParam),
  {
    getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
    getPreviousPageParam: (firstPage, pages) => firstPage.prevCursor,
  }
)
```

一个方向反转：

```tsx
const {
  data,
  fetchNextPage,
  hasNextPage,
  isFetchingNextPage,
} = useInfiniteQuery(
  'projects',
  ({ pageParam = 0 }) => fetchProjects(pageParam),
  {
    select: data => ({
      pages: [...data.pages].reverse(),
      pageParams: [...data.pageParams].reverse(),
    }),
    getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
  }
)
```

### 无限查询数据现在包含了用于获取这些页面的数组和 pageParams。

这允许更轻松地操纵数据和页面参数，例如，删除数据的第一页以及它的参数：

```tsx
queryClient.setQueryData(['projects'], data => ({
  pages: data.pages.slice(1),
  pageParams: data.pageParams.slice(1),
}))
```

### `useMutation` 现在返回一个对象，而不是数组

尽管旧的方式让我们感觉很温暖，就像我们第一次发现 `useState` 一样，但它们的时间并不长。现在的变更返回一个单一的对象。

```tsx
// 旧的：
const [mutate, { status, reset }] = useMutation()

// 新的：
const { mutate, status, reset } = useMutation()
```

### `mutation.mutate` 不再返回一个 promise

- `[mutate]` 变量已更改为 `mutation.mutate` 函数
- 添加了 `mutation.mutateAsync` 函数

关于这种行为，我们收到了很多问题，因为用户期望 promise 的行为像常规 promise 一样。

因此，`mutate` 函数现在被拆分为 `mutate` 和 `mutateAsync` 函数。

在使用回调时，可以使用 `mutate` 函数：

```tsx
const { mutate } = useMutation({ mutationFn: addTodo })

mutate('todo', {
  onSuccess: data => {
    console.log(data)
  },
  onError: error => {
    console.error(error)
  },
  onSettled: () => {
    console.log('settled')
  },
})
```

在使用 async/await 时，可以使用 `mutateAsync` 函数：

```tsx
const { mutateAsync } = useMutation({ mutationFn: addTodo })

try {
  const data = await mutateAsync('todo')
  console.log(data)
} catch (error) {
  console.error(error)
} finally {
  console.log('settled')
}
```

### `useQuery` 的对象语法现在使用了收缩的配置：

```tsx
// 旧的：
useQuery({
  queryKey: 'posts',
  queryFn: fetchPosts,
  config: { staleTime: Infinity },
})

// 新的：
useQuery({
  queryKey: 'posts',
  queryFn: fetchPosts,
  staleTime: Infinity,
})
```

### 如果设置，`QueryOptions.enabled` 选项必须是一个布尔值（`

true`/`false`）

`enabled` 查询选项现在仅在值为 `false` 时禁用查询。
如果需要，可以使用 `!!userId` 或 `Boolean(userId)` 来转换值，并且如果传递了非布尔值，将抛出一个方便的错误。

### `QueryOptions.initialStale` 选项已被移除

`initialStale` 查询选项已被移除，初始数据现在被视为常规数据。
这意味着如果提供了 `initialData`，默认情况下查询将在挂载时重新获取。
如果不想立即重新获取，可以定义一个 `staleTime`。

### `QueryOptions.forceFetchOnMount` 选项已被 `refetchOnMount: 'always'` 替换

说实话，我们已经积累了太多的 `refetchOn____` 选项，所以这应该清理事情。

### `QueryOptions.refetchOnMount` 选项现在仅适用于其父组件，而不是所有查询观察者

当 `refetchOnMount` 设置为 `false` 时，阻止了其他附加组件在挂载时重新获取。
在版本 3 中，只有设置了该选项的组件不会在挂载时重新获取。

### `QueryOptions.queryFnParamsFilter` 已被移除，以支持新的 `QueryFunctionContext` 对象。

已经移除了 `queryFnParamsFilter` 选项，因为查询函数现在获取一个 `QueryFunctionContext` 对象，而不是查询键。

仍然可以在查询函数内部过滤参数，因为 `QueryFunctionContext` 也包含查询键。

### `QueryOptions.notifyOnStatusChange` 选项已被新的 `notifyOnChangeProps` 和 `notifyOnChangePropsExclusions` 选项取代。

使用这些新选项，可以在更细粒度的级别上配置何时重新渲染组件。

只有在所选数据或错误更改时重新渲染：

```tsx
import { useQuery } from 'react-query'

function User() {
  const { data } = useQuery(['user'], fetchUser, {
    notifyOnChangeProps: ['data', 'error'],
  })
  return <div>Username: {data.username}</div>
}
```

防止在 `isStale` 属性更改时重新渲染：

```tsx
import { useQuery } from 'react-query'

function User() {
  const { data } = useQuery(['user'], fetchUser, {
    notifyOnChangePropsExclusions: ['isStale'],
  })
  return <div>Username: {data.username}</div>
}
```

### `QueryResult.clear()` 函数已重命名为 `QueryResult.remove()`

尽管它被称为 `clear`，但它实际上只是从缓存中删除了查询。现在的名称与功能相匹配。

### `QueryResult.updatedAt` 属性已拆分为 `QueryResult.dataUpdatedAt` 和 `QueryResult.errorUpdatedAt` 属性

由于数据和错误可能同时存在，因此 `updatedAt` 属性已拆分为 `dataUpdatedAt` 和 `errorUpdatedAt`。

### `setConsole()` 已被新的 `setLogger()` 函数取代

```tsx
import { setLogger } from 'react-query'

// 使用 Sentry 记录日志
setLogger({
  error: error => {
    Sentry.captureException(error)
  },
})

// 使用 Winston 记录日志
setLogger(winston.createLogger())
```

### React Native 不再需要覆盖日志记录器

为了在 React Native 中防止在查询失败时显示错误屏幕，必须手动更改 Console：

```tsx
import { setConsole } from 'react-query'

setConsole({
  log: console.log,
  warn: console.warn,
  error: console.warn,
})
```

在版本 3 中，**在 React Native 中使用 React Query 时会自动执行此操作**。

### TypeScript

#### `QueryStatus` 从 [枚举](https://www.typescriptlang.org/docs/handbook/enums.html#string-enums) 更改为 [联合类型](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#union-types)

因此，如果您以前正在检查查询或变异的状态属性与 QueryStatus 枚举属性相对比，那么现在您必须将其与枚举先前保留的每个属性的字符串文字进行比较。

因此，您必须将枚举属性更改为其等效的字符串文字，如下所示：
- `QueryStatus.Idle` -> `'idle'`
- `QueryStatus.Loading` -> `'loading'`
- `QueryStatus.Error` -> `'error'`
- `QueryStatus.Success` -> `'success'`

以下是您必须进行的更改示例：

```diff
- import { useQuery, QueryStatus } from 'react-query';
+ import { useQuery } from 'react-query';

const { data, status } = useQuery(['post', id], () => fetchPost(id))

- if (status === QueryStatus.Loading) {
+ if (status === 'loading') {
  ...
}

- if (status === QueryStatus.Error) {
+ if (status === 'error') {
  ...
}
```

## 新功能

#### 查询数据选择器

现在，`useQuery` 和 `useInfiniteQuery` 钩子都有一个 `select` 选项，用于选择或转换查询结果的部分。

```tsx
import { useQuery } from 'react-query'

function User() {
  const { data } = useQuery(['user'], fetchUser, {
    select: user => user.username,
  })
  return <div>Username: {data}</div>
}
```

将 `notifyOnChangeProps` 选项设置为 `['data', 'error']`，仅在所选数据更改时重新渲染。

#### `useQueries()` 钩子，用于可变长度的并行查询执行

希望能在循环中运行 `useQuery` 吗？Hooks 的规则说不行，但是通过新的 `useQueries()` 钩子，您可以实现！

```tsx
import { useQueries } from 'react-query'

function Overview() {
  const results = useQueries([
    { queryKey: ['post', 1], queryFn: fetchPost },
    { queryKey: ['post', 2], queryFn: fetchPost },
  ])
  return (
    <ul>


      {results.map((result, i) => (
        <li key={i}>{result.data.title}</li>
      ))}
    </ul>
  )
}
```

#### `useIsFetching()` 的查询过滤支持

```tsx
import { useIsFetching } from 'react-query'

const isFetching = useIsFetching(['todos'])
```

#### `usePaginatedQuery()` 已被删除，以支持 `keepPreviousData` 选项

新的 `keepPreviousData` 选项适用于 `useQuery` 和 `useInfiniteQuery`，并且对数据具有相同的 "滞后" 效果。

#### 使用全局配置前创建一个 `QueryClient` 的示例

```tsx
import { QueryClient, QueryClientProvider } from 'react-query'

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      // 查询选项
    },
    mutations: {
      // 变更选项
    },
  },
})

export function App() {
  return (
    <QueryClientProvider client={queryClient}>
      {/* ... */}
    </QueryClientProvider>
  )
}
```

## SSR 改进

#### `hydrate` 选项现在是默认情况下启用的，无需手动传递 `hydrate` 选项

```tsx
import { dehydrate, hydrate } from 'react-query'

// 在服务器上
const dehydratedState = dehydrate(queryClient)

// 在客户端上
hydrate(queryClient, dehydratedState)
```

#### `getServerProps` 现在返回 `props`, `queryClient`, 和 `dehydratedState`

```tsx
import { dehydrate, QueryClient } from 'react-query'

export async function getServerProps() {
  const queryClient = new QueryClient()

  await queryClient.prefetchQuery('posts', fetchPosts)

  return {
    props: {
      // 将查询客户端序列化并嵌入到页面
      dehydratedState: dehydrate(queryClient),
    },
  }
}

export default function Page(props) {
  return <QueryClientProvider client={queryClient}>{/* ... */}</QueryClientProvider>
}
```

### `<Hydrate />` 组件

通过新的 `<Hydrate />` 组件，您可以更容易地控制何时应用程序开始和停止混合。

```tsx
import { Hydrate } from 'react-query'

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Hydrate state={dehydratedState}>
        <Root />
      </Hydrate>
    </QueryClientProvider>
  )
}
```

### 跨请求共享的查询/变更

使用全局 `queryKey` 和 `queryHash` 对象，您现在可以更轻松地在请求之间共享查询/变更。

```tsx
import { useQuery } from 'react-query'

export default function Page() {
  const { data } = useQuery(globalQueryKey, fetchSomething)
  return <div>{data}</div>
}

// 在另一个地方
import { useQueryClient } from 'react-query'

function OtherComponent() {
  const queryClient = useQueryClient()
  const { data } = queryClient.getQueryData(globalQueryKey)
  return <div>{data}</div>
}
```

### 高级引导

使用新的 `setErrorHandler` 函数，您可以更好地控制错误处理。

```tsx
import { setErrorHandler } from 'react-query'

setErrorHandler((error: Error) => {
  if (error.message === 'Failed to fetch') {
    // 执行一些操作
  }
})
```

### 在 React 之外观察查询/变更

```tsx
import { createQueryObserver } from 'react-query'

const observer = createQueryObserver(queryClient, {
  queryKey: ['todos'],
})

observer.subscribe({
  onQueryUpdate(data) {
    console.log('Data updated!', data)
  },
})
```

### 其他

#### 查询键将通过 `stringify` 方法进行序列化

这在某些情况下可能会更好地支持现实世界的查询键（例如，使用带有日期的对象）。

#### 默认情况下，不再为查询键计算哈希。

这将提高初始渲染速度，但如果查询键是一个函数，您可能需要自己计算哈希。

#### 新的 `QueryFunctionContext` 对象包含 `queryKey`、`pageParam` 和 `options`。

在 `useQuery` 和 `useInfiniteQuery` 中，第二个参数不再是查询键，而是一个 `QueryFunctionContext` 对象。这对于处理多个参数的查询函数非常有用。

## 新文档网站

由于版本 3 中的所有新功能和更改，我们已经对文档进行了全面的改进和更新。

感谢社区的所有帮助，特别是那些帮助我们完成了所有新的示例！

新文档网站的 Beta 版可在 [https://react-query.tanstack.com/](https://react-query.tanstack.com/) 上获得。

请继续帮助我们制作所有新示例的 PR！

## 迁移到 React Query 3

我们为您提供了一个全面的 [迁移指南](./migrating-to-react-query-3)，指导您完成从 v2 到 v3 的迁移。

## 之后是什么？

我们计划在接下来的几个月中继续改进 React Query 的性能，开发工作流程和工具，并进一步深化框架和库之间的整合。我们希望您继续为此项目做出贡献，无论是通过代码、文档、社区支持还是赞助，都将非常感谢。

我们有一个长期的 [计划](https://github.com/tanstack/react-query/projects/1)，记录了我们接下来想要解决的问题和计划中的功能。

感谢您使用 React Query，以及社区在过去几年中的支持！

## 致谢

在发布这个版本之前，我们有幸得到了许多贡献者的帮助。无论是提交问题、解决错误、改进文档、参与讨论，还是撰写和修复文档，您都是这个项目的一部分，这个版本没有您的帮助是无法完成的。

### 贡

献者

感谢 [GitHub 上的所有贡献者](https://github.com/tanstack/react-query/graphs/contributors)！

特别感谢 [James Kyle](https://github.com/thejameskyle) 和 [John Otander](https://github.com/johno) 的 [downshift](https://github.com/downshift-js/downshift) 项目，它在很大程度上启发了 React Query 的查询键和哈希算法。

### 赞助商

特别感谢以下赞助商为项目提供支持。您的捐赠使我们能够继续维护和改进这个项目。

- [Vercel](https://vercel.com)
- [Sentry](https://sentry.io)
- [Mux](https://mux.com)
- [Thinkmill](https://thinkmill.com.au)

如果您的公司正在使用 React Query 并且希望支持项目，请考虑通过赞助或捐款支持我们。您可以在 [GitHub 赞助页面](https://github.com/sponsors/tannerlinsley) 上找到更多信息。

## 随后的版本

接下来的版本将集中在性能、工具、开发工作流程和文档上。以下是一些可能的特性和改进，可能会在未来的版本中看到：

- 更多 SSR 改进
- 更多 React Native 改进
- 更好的自定义查询
- 更好的热重载和开发工作流程支持
- 更多测试实用工具和指南
- 更多的插件和开发者工具
- 更好的服务端错误日志记录
- 更多演示和样例
- 更好的文件上传/下载支持
- 更多的无限列表和分页示例
- 更多的社区贡献

## 后续步骤

尽管我们努力保持版本更改的最低化，但一些可能需要您进行的操作可能会因您使用的特定特性而异。以下是您可能希望考虑的一些后续步骤。

1. **查看 [迁移指南](./migrating-to-react-query-3) 并完成迁移。**根据您的应用程序的情况，可能需要一些调整。

2. **阅读新的文档。**版本 3 的文档进行了全面的更新和改进，以反映所有新功能和更改。您可以在 [https://react-query.tanstack.com/](https://react-query.tanstack.com/) 找到新文档网站。

3. **更新您的应用程序代码。**根据您的应用程序的情况，您可能需要更新 `useQuery`、`useMutation` 和其他 React Query 钩子的用法。确保您的应用程序能够充分利用新功能和改进。

4. **提供反馈。**如果您在使用 React Query 3 时遇到任何问题、错误或有建议，请在 [GitHub 存储库](https://github.com/tanstack/react-query) 上创建问题。您的反馈对我们非常重要，有助于我们改进项目。

5. **升级和迁移其他库。**如果您的应用程序使用其他与 React Query 相关的库，如 React Query Devtools、React Query Native、React Query Test Utils 等，您可能还需要对它们进行升级和迁移。请查阅这些库的文档，以获取关于如何迁移到 React Query 3 的指导。

感谢您的支持和使用，希望您在 React Query 3 中体验到更好的开发体验和性能！如有问题或疑问，欢迎随时提问。祝您的前端开发之旅愉快！
