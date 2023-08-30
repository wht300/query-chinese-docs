---
title: Solid Query
---

`@tanstack/solid-query` 包为在 SolidJS 中使用 TanStack Query 提供了一流的 API。

## 示例

```tsx
import { QueryClient, QueryClientProvider, createQuery } from '@tanstack/solid-query'
import { Switch, Match, For } from 'solid-js'

const queryClient = new QueryClient()

function Example() {
  const query = createQuery(() => ['todos'], fetchTodos)

  return (
    <div>
      <Switch>
        <Match when={query.isLoading}>
          <p>加载中...</p>
        </Match>
        <Match when={query.isError}>
          <p>错误：{query.error.message}</p>
        </Match>
        <Match when={query.isSuccess}>
          <For each={query.data}>
            {(todo) => <p>{todo.title}</p>}
          </For>
        </Match>
      </Switch>
    </div>
  )
}

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
  )
}
```

## 可用函数

Solid Query 提供了一些实用的原语和函数，可以使在 SolidJS 应用程序中管理服务器状态更加容易。

- `createQuery`
- `createQueries`
- `createInfiniteQueries`
- `createMutation`
- `useIsFetching`
- `useIsMutating`
- `useQueryClient`
- `QueryClient`
- `QueryClientProvider`

## Solid Query 与 React Query 之间的重要区别

Solid Query 提供了类似于 React Query 的 API，但也有一些关键区别需要注意。

- 为了保持它们的响应性，在使用 `createQuery`、`createQueries`、`createInfiniteQuery` 和 `useIsFetching` 时，查询键需要包装在一个函数内。

```tsx
// ❌ React 版本
useQuery(["todos", todo], fetchTodos)

// ✅ Solid 版本
createQuery(() => ["todos", todo()], fetchTodos)
```

- 如果你在 `<Suspense>` 边界内访问查询数据，悬停（Suspense）会自动工作。

```tsx
import { For, Suspense } from 'solid-js'

function Example() {
  const query = createQuery(() => ['todos'], fetchTodos)
  return (
    <div>
      {/* ✅ 将触发加载回退，数据在 suspense 上下文中访问。 */}
      <Suspense fallback={"加载中..."}>
        <For each={query.data}>{(todo) => <div>{todo.title}</div>}</For>
      </Suspense>
      {/* ❌ 不会触发加载回退，数据不在 suspense 上下文中访问。 */}
      <For each={query.data}>{(todo) => <div>{todo.title}</div>}</For>
    </div>
  )
}
```

- Solid Query 原语（`createX`）不支持解构。从这些函数返回的值是一个 store，在响应性上下文中才会被追踪。

```tsx
import { QueryClient, QueryClientProvider, createQuery } from '@tanstack/solid-query'
import { Match, Switch } from 'solid-js'

const queryClient = new QueryClient()

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
  )
}

function Example() {
  // ❌ React 版本-- 支持在响应性上下文之外进行解构
  // const { isLoading, error, data } = useQuery(['repoData'], () =>
  //   fetch('https://api.github.com/repos/tannerlinsley/react-query').then(res =>
  //     res.json()
  //   )
  // )

  // ✅ Solid 版本-- 在响应性上下文之外不支持解构
  const query = createQuery(
    () => ['repoData'],
    () =>
      fetch('https://api.github.com/repos/tannerlinsley/react-query').then(
        (res) => res.json(),
      ),
  )

  // ✅ 在 JSX 响应性上下文中访问查询属性
  return (
    <Switch>
      <Match when={query.isLoading}>加载中...</Match>
      <Match when={query.isError}>错误：{query.error.message}</Match>
      <Match when={query.isSuccess}>
        <div>
          <h1>{query.data.name}</h1>
          <p>{query.data.description}</p>
          <strong>👀 {query.data.subscribers_count}</strong>{' '}
          <strong>✨ {query.data.stargazers_count}</strong>{' '}
          <strong>🍴 {query.data.forks_count}</strong>
        </div>
      </Match>
    </Switch>
  )
}
```

- 如果你希望选项是响应性的，你需要使用对象获取器语法将它们传递。这一开始可能看起来有些奇怪，但它会导致更符合 Solid 代码习惯的代码。

```tsx
import {
  QueryClient,
  QueryClientProvider,
  createQuery,
} from '@tanstack/solid-query'
import { createSignal, For } from 'solid-js'

const queryClient = new QueryClient()

function Example() {
  const [enabled, setEnabled] = createSignal(false)
  const query = createQuery(() => ['todos'], fetchTodos, {
    // ❌ 直接传递一个 signal 是不会响应的
    // enabled: enabled(),

    // ✅ 传递返回 signal 的函数是响应的
    get enabled() {
      return enabled()
    },
  })

  return (
    <div>
      <Switch>
        <Match when={query.isLoading}>
          <p>加载中...</p>
        </Match>
        <Match when={query.isError}>
          <p>错误：{query.error.message}</p>
        </Match>
        <Match when={query.isSuccess}>
          <For each={query.data}>
            {(todo) => <p>{todo.title}</p>}
          </For>
        </Match>
      </Switch>
      <button onClick={() => setEnabled(!enabled())}>切换启用状态</button>
    </div>
  )
}

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
  )
}
```

- 错误可以通过 SolidJS 的本机 `ErrorBoundary` 组件捕获和重置。Solid Query 不需要 `QueryErrorResetBoundary`。

- 由于属性跟踪是通过 Solid 的精细粒度响应性来处理的，因此不需要像 `notifyOnChangeProps`

这样的选项。
