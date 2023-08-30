---
id: suspense
title: React Suspense 模式
---

> 注意：React Query 的 Suspense 模式是实验性的，就像数据获取本身的 Suspense 一样。这些 API 将会发生变化，除非你将 React 和 React Query 版本都锁定在彼此兼容的补丁级版本，否则不应在生产环境中使用。

React Query 也可以与 React 的新数据获取 API 的 Suspense 模式一起使用。要启用此模式，你可以将全局或查询级别的配置中的 `suspense` 选项设置为 `true`。

全局配置示例：

```tsx
// 针对所有查询进行配置
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      suspense: true,
    },
  },
})

function Root() {
  return (
    <QueryClientProvider client={queryClient}>
      <App />
    </QueryClientProvider>
  )
}
```

查询配置示例：

```tsx
import { useQuery } from '@tanstack/react-query'

// 为单个查询启用
useQuery({ queryKey, queryFn, suspense: true })
```

在使用 Suspense 模式时，`status` 状态和 `error` 对象是不需要的，会被 `React.Suspense` 组件所取代（包括使用 `fallback` 属性和 React 错误边界来捕获错误）。请阅读 [重置错误边界](#resetting-error-boundaries) 并查看 [Suspense 示例](https://codesandbox.io/s/github/tannerlinsley/react-query/tree/main/examples/react/suspense) 以获取有关如何设置 Suspense 模式的更多信息。

除了在 Suspense 模式下查询行为的不同之外，变更操作（mutations）的行为也有一些不同。默认情况下，在变更操作失败时，不会提供 `error` 变量，而是会在使用它的组件的下一次渲染中抛出，并传播到最近的错误边界，类似于查询错误。如果你希望禁用此行为，可以将 `useErrorBoundary` 选项设置为 `false`。如果希望根本不抛出错误，还可以将 `throwOnError` 选项同样设置为 `false`！

## 重置错误边界

无论是在查询中使用 **suspense** 还是 **useErrorBoundaries**，都需要一种方法在发生错误后重新渲染时通知查询要重试。

查询错误可以通过 `QueryErrorResetBoundary` 组件或 `useQueryErrorResetBoundary` 钩子来重置。

在使用组件时，它会在组件范围内重置任何查询错误：

```tsx
import { QueryErrorResetBoundary } from '@tanstack/react-query'
import { ErrorBoundary } from 'react-error-boundary'

const App: React.FC = () => (
  <QueryErrorResetBoundary>
    {({ reset }) => (
      <ErrorBoundary
        onReset={reset}
        fallbackRender={({ resetErrorBoundary }) => (
          <div>
            出现了错误！
            <Button onClick={() => resetErrorBoundary()}>重试</Button>
          </div>
        )}
      >
        <Page />
      </ErrorBoundary>
    )}
  </QueryErrorResetBoundary>
)
```

在使用钩子时，它会在最近的 `QueryErrorResetBoundary` 内重置任何查询错误。如果没有定义边界，它会在全局范围内重置它们：

```tsx
import { useQueryErrorResetBoundary } from '@tanstack/react-query'
import { ErrorBoundary } from 'react-error-boundary'

const App: React.FC = () => {
  const { reset } = useQueryErrorResetBoundary()
  return (
    <ErrorBoundary
      onReset={reset}
      fallbackRender={({ resetErrorBoundary }) => (
        <div>
          出现了错误！
          <Button onClick={() => resetErrorBoundary()}>重试</Button>
        </div>
      )}
    >
      <Page />
    </ErrorBoundary>
  )
}
```

## 渲染触发获取 vs 边获取边渲染

在 `suspense` 模式下，React Query 默认作为一种**渲染触发获取**的解决方案工作，无需额外配置。这意味着当你的组件尝试挂载时，它们将触发查询获取并暂停，但只有在你导入它们并将它们挂载后才会这样。如果你想将其提升到更高级别，并实现**边获取边渲染**模型，我们建议在路由回调和/或用户交互事件上实施[预获取](../guides/prefetching)，以在挂载查询之前开始加载查询，甚至希望在导入或挂载其父组件之前开始加载查询。

## 进一步阅读

有关使用 suspense 选项的提示，请查看社区资源中的 [Suspensive React Query 包](../community/suspensive-react-query)。
