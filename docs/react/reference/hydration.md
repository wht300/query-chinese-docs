---
id: hydration
title: 水合 (Hydration)
---

## `dehydrate`

`dehydrate` 创建一个被冻结的 `cache` 表示，稍后可以使用 `Hydrate`、`useHydrate` 或 `hydrate` 进行水合。这在将预取的查询从服务器传递到客户端，或将查询持久化到 localStorage 或其他持久位置时非常有用。默认情况下，它只包含当前成功的查询。

```tsx
import { dehydrate } from '@tanstack/react-query'

const dehydratedState = dehydrate(queryClient, {
  shouldDehydrateQuery,
})
```

**选项**

- `client: QueryClient`
  - **必需**
  - 要进行水合的 `queryClient`
- `options: DehydrateOptions`
  - 可选
  - `dehydrateMutations: boolean`
    - 可选
    - 是否对突变进行水合。
  - `dehydrateQueries: boolean`
    - 可选
    - 是否对查询进行水合。
  - `shouldDehydrateMutation: (mutation: Mutation) => boolean`
    - 可选
    - 对缓存中的每个突变调用此函数
    - 返回 `true` 以在水合中包括此突变，或返回 `false` 否则
    - 默认版本只包括已暂停的突变
    - 如果您想在保留以前行为的同时扩展函数，请导入并在返回语句中执行 `defaultShouldDehydrateMutation`
  - `shouldDehydrateQuery: (query: Query) => boolean`
    - 可选
    - 对缓存中的每个查询调用此函数
    - 返回 `true` 以在水合中包括此查询，或返回 `false` 否则
    - 默认版本只包括成功的查询，通过 `shouldDehydrateQuery: () => true` 可以包括所有查询
    - 如果您想在保留以前行为的同时扩展函数，请导入并在返回语句中执行 `defaultShouldDehydrateQuery`

**返回值**

- `dehydratedState: DehydratedState`
  - 包括在稍后的某个时候水合 `queryClient` 所需的一切内容
  - 您 **不应该** 依赖于此响应的确切格式，它不是公共 API 的一部分，可以随时更改
  - 此结果不是以序列化形式呈现的，如果需要，您需要自行处理

### 限制

某些存储系统（如浏览器的 [Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)）要求值能够序列化为 JSON。如果您需要对不自动序列化为 JSON 的值（如 `Error` 或 `undefined`）进行水合，您必须自行序列化它们。由于默认情况下只包括成功的查询，如果还要包括 `Errors`，您必须提供 `shouldDehydrateQuery`，例如：

```tsx
// 服务器
const state = dehydrate(client, { shouldDehydrateQuery: () => true }) // 也包括 Errors
const serializedState = mySerialize(state) // 将 Error 实例转换为对象

// 客户端
const state = myDeserialize(serializedState) // 将对象转换回 Error 实例
hydrate(client, state)
```

## `hydrate`

`hydrate` 将之前水合的状态添加到 `cache` 中。

```tsx
import { hydrate } from '@tanstack/react-query'

hydrate(queryClient, dehydratedState, options)
```

**选项**

- `client: QueryClient`
  - **必需**
  - 要将状态水合到的 `queryClient`
- `dehydratedState: DehydratedState`
  - **必需**
  - 要水合到客户端的状态
- `options: HydrateOptions`
  - 可选
  - `defaultOptions: DefaultOptions`
    - 可选
    - `mutations: MutationOptions` 用于水合突变的默认突变选项。
    - `queries: QueryOptions` 用于水合查询的默认查询选项。
  - `context?: React.Context<QueryClient | undefined>`
    - 使用此选项可以使用自定义的 React Query 上下文。否则将使用 `defaultContext`。

### 限制

如果水合中包含的查询已经存在于 queryCache 中，`hydrate` 不会覆盖它们，并且它们将被**静默**丢弃。

[//]: # 'useHydrate'

## `useHydrate`

`useHydrate` 将之前水合的状态添加到由 `useQueryClient()` 返回的 `queryClient` 中。如果客户端已经包含数据，新的查询将根据更新时间戳进行智能合并。

```tsx
import { useHydrate } from '@tanstack/react-query'

useHydrate(dehydratedState, options)
```

**选项**

- `dehydratedState: DehydratedState`
  - **必需**
  - 要水合的状态
- `options: HydrateOptions`
  - 可选
  - `defaultOptions: QueryOptions`
    - 用于水合查询的默认查询选项。
  - `context?: React.Context<QueryClient | undefined>`
    - 使用此选项可以使用自定义的 React Query 上下文。否则将使用 `defaultContext`。

[//]: # 'useHydrate'
[//]: # 'Hydrate'

## `Hydrate`

`Hydrate` 将 `useHydrate` 包装成组件。在您需要在类组件中进行水合，或者需要在与 `QueryClientProvider` 渲染的同一级别进行水合时，可能会很有用。

```tsx
import { Hydrate } from '@tanstack/react-query'

function App() {
  return <Hydrate state={dehydratedState}>...</Hydrate>
}
```

**选项**

- `state: DehydratedState`
  - 要水合的状态
- `options: HydrateOptions`
  - 可选
  - `defaultOptions: QueryOptions`
    - 用于水合查询的默认查询选项。
  - `context?: React

.Context<QueryClient | undefined>`
- 使用此选项可以使用自定义的 React Query 上下文。否则将使用 `defaultContext`。

[//]: # 'Hydrate'
