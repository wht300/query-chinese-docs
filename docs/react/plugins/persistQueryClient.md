---
id: persistQueryClient
title: persistQueryClient
---

这是一组与“持久化器”（persister）交互的实用工具，用于将您的 queryClient 保存以供以后使用。不同的 **持久化器** 可用于将您的客户端和缓存存储到许多不同的存储层。

## 构建持久化器

- [createSyncStoragePersister](../plugins/createSyncStoragePersister)
- [createAsyncStoragePersister](../plugins/createAsyncStoragePersister)
- [创建自定义持久化器](#持久化器)

## 工作原理

**重要** - 为了使持久化正常工作，您可能希望在 hydration 期间（如上所示）将 `QueryClient` 传递一个 `cacheTime` 值来覆盖默认值。

如果在创建 `QueryClient` 实例时未设置它，它将在 hydration 时默认为 `300000`（5 分钟），并且存储的缓存将在 5 分钟的不活动时间后被丢弃。这是默认的垃圾收集行为。

它应该设置为与 `persistQueryClient` 的 `maxAge` 选项相同或更高的值。例如，如果 `maxAge` 是 24 小时（默认值），那么 `cacheTime` 应该是 24 小时或更长。如果低于 `maxAge`，则会启动垃圾收集并在预期之前丢弃存储的缓存。

您还可以将其设置为 `Infinity` 以完全禁用垃圾收集行为。

```tsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      cacheTime: 1000 * 60 * 60 * 24, // 24 小时
    },
  },
})
```

### 缓存破坏

有时您可能会对您的应用程序或数据进行更改，从而立即使任何和所有缓存的数据无效。如果发生这种情况，您可以传递一个 `buster` 字符串选项。如果找到的缓存不包含该 buster 字符串，它将被丢弃。以下几个函数接受此选项：

```tsx
persistQueryClient({ queryClient, persister, buster: buildHash })
persistQueryClientSave({ queryClient, persister, buster: buildHash })
persistQueryClientRestore({ queryClient, persister, buster: buildHash })
```

### 移除

如果发现数据是以下内容之一：

1. 过期（请参见 `maxAge`）
2. 破坏（请参见 `buster`）
3. 错误（例如，`throws ...`）
4. 空（例如，`undefined`）

持久化器 `removeClient()` 将被调用，并且缓存将被立即丢弃。

## API

### `persistQueryClientSave`

- 您的查询/突变会被 [`dehydrated`](../reference/hydration#dehydrate) 并由您提供的持久化器存储。
- `createSyncStoragePersister` 和 `createAsyncStoragePersister` 会将此操作最多每秒进行一次，以节省潜在的昂贵的写入操作。请查阅它们的文档以了解如何自定义其节流时间。

您可以在您选择的时刻显式地持久化缓存。

```tsx
persistQueryClientSave({
  queryClient,
  persister,
  buster = '',
  dehydrateOptions = undefined,
})
```

### `persistQueryClientSubscribe`

当您的 `queryClient` 的缓存发生更改时，会运行 `persistQueryClientSave`。例如：您可以在用户登录并勾选“记住我”时启动 `subscribe`。

- 它返回一个 `unsubscribe` 函数，您可以使用该函数停止监视器；终止对持久化缓存的更新。
- 如果您希望在 `unsubscribe` 后擦除持久化缓存，可以向 `persistQueryClientRestore` 发送新的 `buster`，这将触发持久化器的 `removeClient` 函数并且丢弃持久化的缓存。

```tsx
persistQueryClientSubscribe({
  queryClient,
  persister,
  buster = '',
  dehydrateOptions = undefined,
})
```

### `persistQueryClientRestore`

- 尝试从持久化器中恢复先前持久化的脱水查询/突变缓存，并将其放回传递的查询客户端的查询缓存中。
- 如果找到的缓存的年龄超过了 `maxAge`（默认为 24 小时），它将被丢弃。此时间可以根据您的需求进行自定义。

您可以在选择的时刻使用它来恢复缓存。

```tsx
persistQueryClientRestore({
  queryClient,
  persister,
  maxAge = 1000 * 60 * 60 * 24, // 24 小时
  buster = '',
  hydrateOptions = undefined,
})
```

### `persistQueryClient`

执行以下操作：

1. 立即恢复任何持久化的缓存（[参见 `persistQueryClientRestore`](#persistqueryclientrestore)）
2. 订阅查询缓存，并返回 `unsubscribe` 函数（[参见 `persistQueryClientSubscribe`](#persistqueryclientsubscribe)）。

这个功能在版本 3.x 中保留下来。

```tsx
persistQueryClient({
  queryClient,
  persister,
  maxAge = 1000 * 60 * 60 * 24, // 24 小时
  buster = '',
  hydrateOptions = undefined,
  dehydrateOptions = undefined,
})
```

### `Options`

所有可用选项如下：

```tsx
interface PersistQueryClientOptions {
  /** 要持久化的 QueryClient */
  queryClient: QueryClient
  /** 用于存储和恢复缓存的 Persister 接口
   * 到/从持久位置 */
  persister: Persister


  /** 缓存的最大允许年龄（以毫秒为单位）。
   * 如果找到的持久缓存的年龄超过此时间，
   * 它将**静默地**被丢弃
   * （默认为 24 小时） */
  maxAge?: number
  /** 一个用于强制使现有缓存失效的唯一字符串。
   * 如果缓存不共享相同的 buster 字符串，则会触发缓存丢弃 */
  buster?: string
  /** 传递给 hydrate 函数的选项
   * 不在 `persistQueryClientSave` 或 `persistQueryClientSubscribe` 上使用 */
  hydrateOptions?: HydrateOptions
  /** 传递给 dehydrate 函数的选项
  * 不在 `persistQueryClientRestore` 上使用 */
  dehydrateOptions?: DehydrateOptions
}
```

实际上有三个可用的接口：
- `PersistedQueryClientSaveOptions` 用于 `persistQueryClientSave` 和 `persistQueryClientSubscribe`（不使用 `hydrateOptions`）。
- `PersistedQueryClientRestoreOptions` 用于 `persistQueryClientRestore`（不使用 `dehydrateOptions`）。
- `PersistQueryClientOptions` 用于 `persistQueryClient`

## 在 React 中的使用

[persistQueryClient](#persistQueryClient) 将尝试恢复缓存并自动订阅进一步的更改，从而将您的客户端同步到所提供的存储。

然而，恢复是异步的，因为所有的持久化器本质上都是异步的，这意味着如果在恢复时渲染您的应用程序，您可能会遇到竞争条件，如果一个查询在同时挂载和获取数据。

此外，如果在 React 组件生命周期之外订阅更改，则无法取消订阅：

```tsx
// 🚨 永远不会取消同步
persistQueryClient({
  queryClient,
  persister: localStoragePersister,
})

// 🚨 与恢复同时发生
ReactDOM.createRoot(rootElement).render(<App />)
```

### PersistQueryClientProvider

对于这种情况，您可以使用 `PersistQueryClientProvider`。它将确保根据 React 组件生命周期正确地订阅/取消订阅，并且还将确保查询不会在我们仍在恢复时开始获取。但是，查询仍然会渲染，只是会将其放入 `fetchingState: 'idle'`，直到数据恢复为止。然后，它们将重新获取数据，除非恢复的数据足够_新鲜_，并且还将尊重 _initialData_。它可以 _替代_ 正常的 [QueryClientProvider](../reference/QueryClientProvider)：

```tsx

import { PersistQueryClientProvider } from '@tanstack/react-query-persist-client'
import { createSyncStoragePersister } from '@tanstack/query-sync-storage-persister'

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      cacheTime: 1000 * 60 * 60 * 24, // 24 小时
    },
  },
})

const persister = createSyncStoragePersister({
  storage: window.localStorage,
})

ReactDOM.createRoot(rootElement).render(
  <PersistQueryClientProvider
    client={queryClient}
    persistOptions={{ persister }}
  >
    <App />
  </PersistQueryClientProvider>
)
```

#### Props

`PersistQueryClientProvider` 接受与 [QueryClientProvider](../reference/QueryClientProvider) 相同的 props，以及：

- `persistOptions: PersistQueryClientOptions`
  - 您可以传递给 [persistQueryClient](#persistqueryclient) 的所有 [选项](#options)，除了 QueryClient 本身
- `onSuccess?: () => void`
  - 可选的
  - 在初始恢复完成时将被调用
  - 可用于 [resumePausedMutations](../reference/QueryClient#queryclientresumepausedmutations)

### useIsRestoring

如果您正在使用 `PersistQueryClientProvider`，您还可以与其一起使用 `useIsRestoring` 钩子，以检查恢复是否正在进行中。`useQuery` 和类似的函数在内部也会检查这一点，以避免恢复和挂载查询之间的竞争条件。

## 持久化器

### 持久化器接口

持久化器具有以下接口：

```tsx
export interface Persister {
  persistClient(persistClient: PersistedClient): Promisable<void>
  restoreClient(): Promisable<PersistedClient | undefined>
  removeClient(): Promisable<void>
}
```

持久化的客户端条目具有以下接口：

```tsx
export interface PersistedClient {
  timestamp: number
  buster: string
  cacheState: any
}
```

您可以导入这些接口（用于构建持久化器）：
```tsx
import { PersistedClient, Persister } from "@tanstack/react-query-persist-client";
```

### 构建持久化器

您可以按照您的喜好进行持久化。以下是构建 [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 持久化器的示例。与 `Web Storage API` 相比，IndexedDB 更快，可以存储超过 5MB 的数据，并且不需要序列化。这意味着它可以轻松存储 JavaScript 原生类型，如 `Date` 和 `File`。

```tsx
import { get, set, del } from "idb-keyval";
import { PersistedClient, Persister } from "@tanstack/react-query-persist-client";

/**
 * 创建一个 IndexedDB 持久化器
 * @see https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API
 */
export function createIDBPersister(idbValidKey: IDBValidKey = "reactQuery") {
  return {
    persistClient: async (client: PersistedClient) => {
      set(idbValidKey, client);
    },
    restoreClient: async () => {
      return await get<PersistedClient>(idbValidKey);
    },
    removeClient: async () => {
      await del(idbValidKey);
    },
  } as Persister;
}
```
