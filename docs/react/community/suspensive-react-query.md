---
id: suspensive-react-query
title: 悬停式 React Query 中文翻译
---

使用类型安全的 useQuery、useQueries、useInfiniteQuery，并附带默认 suspense 选项。

使用 @suspensive/react-query，将加载和错误处理的委托从组件的内部移至外部，通过 [useSuspenseQuery](https://suspensive.org/docs/react-query/src/useSuspenseQuery.i18n)、[useSuspenseQueries](https://suspensive.org/docs/react-query/src/useSuspenseQueries.i18n) 和 [useSuspenseInfiniteQuery](https://suspensive.org/docs/react-query/src/useSuspenseInfiniteQuery.i18n)，并专注于组件内的成功。

你甚至不需要使用 isSuccess 标志。

## 安装

你可以通过 [NPM](https://www.npmjs.com/package/@suspensive/react-query) 安装 @suspensive/react-query。

```bash
$ npm i @suspensive/react-query
# 或者
$ pnpm add @suspensive/react-query
# 或者
$ yarn add @suspensive/react-query
```

### 动机

如果在 @tanstack/react-query 中打开了 suspense 模式，你可以在 Suspense 和 ErrorBoundary 中使用 useQuery。

```tsx
import { useQuery } from '@tanstack/react-query'

const Example = () => {
  const query = useQuery({
    queryKey,
    queryFn,
    suspense: true,
  })

  query.data // TData | undefined

  if (query.isSuccess) {
    query.data // TData
  }
}
```

通常情况下，query.data 会像上面的代码示例一样是 `TData | undefined`。但实际上 useQuery 的返回类型 query.data 会始终被填充，因为它的父组件是 [Suspense](https://suspensive.org/docs/react/src/Suspense.i18n) 和 [ErrorBoundary](https://suspensive.org/docs/react/src/ErrorBoundary.i18n)。

这就是为什么 @suspensive/react-query 提供了 **useSuspenseQuery**

## useSuspenseQuery

这个钩子的返回类型没有 isLoading、isError 属性，因为 Suspense 和 ErrorBoundary 会保证这个钩子的数据。

此外，这个钩子的选项有默认的 suspense: true。你可以像 @tanstack/react-query 的 useQuery 一样为这个钩子提供新的选项。

```tsx
import { useSuspenseQuery } from '@suspensive/react-query'

const Example = () => {
  const query = useSuspenseQuery({
    queryKey,
    queryFn,
  }) // suspense:true 是默认值。

  // 不需要通过 isSuccess 进行类型缩小
  query.data // TData
}
```

### 专注于成功

现在，我们可以将组件的注意力集中在只有在组件中始终成功的任何获取上。

### 更多信息

请查阅 [Suspensive 官方文档网站](https://suspensive.org/) 的完整文档，同时欢迎在 [Suspensive GitHub](https://github.com/suspensive/react) 上进行 Pull Request。
