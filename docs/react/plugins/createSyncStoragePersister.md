---
id: createSyncStoragePersister
title: createSyncStoragePersister
---

## 安装

此实用程序作为一个独立的包提供，在 `'@tanstack/query-sync-storage-persister'` 导入下可用。
```bash
npm install @tanstack/query-sync-storage-persister @tanstack/react-query-persist-client
```
或者
```bash
pnpm add @tanstack/query-sync-storage-persister @tanstack/react-query-persist-client
```
或者
```bash
yarn add @tanstack/query-sync-storage-persister @tanstack/react-query-persist-client
```

## 使用方法

- 导入 `createSyncStoragePersister` 函数
- 创建一个新的 syncStoragePersister
- 将其传递给 [`persistQueryClient`](../plugins/persistQueryClient)

```tsx
import { persistQueryClient } from '@tanstack/react-query-persist-client'
import { createSyncStoragePersister } from '@tanstack/query-sync-storage-persister'

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      cacheTime: 1000 * 60 * 60 * 24, // 24 hours
    },
  },
})

const localStoragePersister = createSyncStoragePersister({ storage: window.localStorage })
// const sessionStoragePersister = createSyncStoragePersister({ storage: window.sessionStorage })

persistQueryClient({
  queryClient,
  persister: localStoragePersister,
})
```

## 重试

持久化可能会失败，例如如果大小超过了存储中可用的空间。可以通过为持久化程序提供 `retry` 函数来优雅地处理错误。

retry 函数接收尝试保存的 `persistedClient`、`error` 和 `errorCount` 作为输入。它应该返回一个 _新的_ `PersistedClient`，用它再次尝试持久化。如果返回 _undefined_，则不会再次尝试持久化。

```tsx
export type PersistRetryer = (props: {
  persistedClient: PersistedClient
  error: Error
  errorCount: number
}) => PersistedClient | undefined
```

### 预定义的策略

默认情况下，不会进行重试。您可以使用预定义的策略之一来处理重试。它们可以从 `from '@tanstack/react-query-persist-client'` 导入：

- `removeOldestQuery`
  - 将返回一个新的 `PersistedClient`，其中删除了最旧的查询。

```tsx
const localStoragePersister = createSyncStoragePersister({
  storage: window.localStorage,
  retry: removeOldestQuery
})
```

## API

### `createSyncStoragePersister`

调用此函数以创建一个 syncStoragePersister，稍后您可以在 `persistQueryClient` 中使用。

```tsx
createSyncStoragePersister(options: CreateSyncStoragePersisterOptions)
```

### `Options`

```tsx
interface CreateSyncStoragePersisterOptions {
  /** 用于从缓存中设置和检索项目的存储客户端 (window.localStorage 或 window.sessionStorage) */
  storage: Storage | undefined | null
  /** 存储缓存时要使用的键 */
  key?: string
  /** 为避免 spamming，
   * 传递一个以毫秒为单位的时间来限制将缓存保存到磁盘 */
  throttleTime?: number
  /** 如何将数据序列化到存储 */
  serialize?: (client: PersistedClient) => string
  /** 如何从存储中反序列化数据 */
  deserialize?: (cachedString: string) => PersistedClient
  /** 如何在出现错误时重试持久化 **/
  retry?: PersistRetryer
}
```

默认选项为：

```tsx
{
  key = `REACT_QUERY_OFFLINE_CACHE`,
  throttleTime = 1000,
  serialize = JSON.stringify,
  deserialize = JSON.parse,
}
```

#### `serialize` 和 `deserialize` 选项
在 `localStorage` 中存储的数据量有限。
如果需要在 `localStorage` 中存储更多数据，可以覆盖 `serialize` 和 `deserialize` 函数，使用类似 [lz-string](https://github.com/pieroxy/lz-string/) 的库对数据进行压缩和解压缩。

```tsx
import { QueryClient } from '@tanstack/react-query';
import { persistQueryClient } from '@tanstack/react-query-persist-client'
import { createSyncStoragePersister } from '@tanstack/query-sync-storage-persister'

import { compress, decompress } from 'lz-string';

const queryClient = new QueryClient({ defaultOptions: { queries: { staleTime: Infinity } } });

persistQueryClient({
  queryClient: queryClient,
  persister: createSyncStoragePersister({
    storage: window.localStorage,
    serialize: data => compress(JSON.stringify(data)),
    deserialize: data => JSON.parse(decompress(data)),
  }),
  maxAge: Infinity,
});
```
