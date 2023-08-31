---
id: overview
title: 概述
---

TanStack Query（前身为 React Query）经常被描述为网络应用程序中缺失的数据获取库，但更多地从技术角度来看，它使得在您的网络应用程序中进行**获取、缓存、同步和更新服务器状态**变得轻而易举。

## 动机

大多数核心网络框架**不提供**一种获取或更新数据的整体方式。因此，开发人员最终要么构建包含严格数据获取观点的元框架，要么发明自己的数据获取方式。这通常意味着将基于组件的状态和副作用拼凑在一起，或者使用更通用的状态管理库来在应用程序中存储和提供异步数据。

尽管大多数传统的状态管理库非常适用于处理客户端状态，但在处理异步或服务器状态方面就不那么出色了。这是因为**服务器状态完全不同**。首先，服务器状态：

- 在您不控制或拥有的远程位置上进行持久存储
- 需要异步 API 来获取和更新
- 意味着共享所有权，并且可以在没有您知识的情况下由其他人更改
- 如果不小心，可能在您的应用程序中变得“过时”

一旦您理解了应用程序中的服务器状态的性质，那么随着您的进一步探索，**将会出现更多的挑战**，例如：

- 缓存...（可能是编程中最难的事情之一）
- 将多次请求相同数据的去重为单个请求
- 后台更新“过时”数据
- 知道何时数据“过时”
- 尽快反映数据更新
- 诸如分页和懒加载数据之类的性能优化
- 管理服务器状态的内存和垃圾收集
- 用结构共享对查询结果进行记忆

如果您对该列表不感到不知所措，那可能意味着您可能已经解决了所有服务器状态问题，并且应该获得一个奖项。然而，如果您和绝大多数人一样，您可能尚未解决所有或大部分这些挑战，而我们只是刚刚触及了表面！

React Query 无疑是管理服务器状态的**最佳**库之一。它在**开箱即用**的情况下工作得非常出色，零配置，并且可以在应用程序增长时进行自定义。

React Query 允许您克服_服务器状态_的棘手挑战和障碍，并在开始控制您的应用程序数据之前控制它。

在技术层面上，React Query 可能会：

- 帮助您从应用程序中移除**许多**复杂且不被理解的代码行，并用仅有几行 React Query 逻辑进行替换。
- 使您的应用程序更易于维护，更容易构建新功能，而不必担心如何连接新的服务器状态数据源
- 对您的最终用户产生直接影响，使您的应用程序比以往更快速和响应。
- 可能帮助您节省带宽并提高内存性能

[//]: # 'Example'

## 足够的说话，快给我看点代码！

在下面的示例中，您可以看到 React Query 在其最基本和简单的形式中被用于获取 React Query GitHub 项目本身的 GitHub 统计信息：

[在 CodeSandbox 中打开](https://codesandbox.io/s/github/tannerlinsley/react-query/tree/main/examples/react/simple)

```tsx
import {
  QueryClient,
  QueryClientProvider,
  useQuery,
} from '@tanstack/react-query'

const queryClient = new QueryClient()

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
  )
}

function Example() {
  const { isLoading, error, data } = useQuery({
    queryKey: ['repoData'],
    queryFn: () =>
      fetch('https://api.github.com/repos/TanStack/query').then(
        (res) => res.json(),
      ),
  })

  if (isLoading) return 'Loading...'

  if (error) return 'An error has occurred: ' + error.message

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.description}</p>
      <strong>👀 {data.subscribers_count}</strong>{' '}
      <strong>✨ {data.stargazers_count}</strong>{' '}
      <strong>🍴 {data.forks_count}</strong>
    </div>
  )
}
```

[//]: # 'Example'
[//]: # 'Materials'

## 你已经说服我了，那接下来怎么办？

- 考虑参加官方的[React Query 课程](https://ui.dev/react-query?from=tanstack)（或为您的整个团队购买！）
- 以自己的步调学习 React Query，使用我们非常详细的[演练指南](../installation)和[API 参考](../reference/useQuery)

[//]: # 'Materials'
