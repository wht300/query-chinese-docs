---
id: createAsyncStoragePersister
title: createAsyncStoragePersister
---

## 安装

此实用程序作为一个独立的包提供，在 `'@tanstack/query-async-storage-persister'` 导入下可用。
```bash
npm install @tanstack/query-async-storage-persister @tanstack/react-query-persist-client
```
或者
```bash
pnpm add @tanstack/query-async-storage-persister @tanstack/react-query-persist-client
```
或者
```bash
yarn add @tanstack/query-async-storage-persister @tanstack/react-query-persist-client
```

## 使用方法

- 导入 `createAsyncStoragePersister` 函数
- 创建一个新的 `asyncStoragePersister`
  - 您可以将任何符合 `AsyncStorage` 接口的 `storage` 传递给它 - 下面的示例使用了 React Native 中的 async-storage
- 使用 [`PersistQueryClientProvider`](../plugins/persistQueryClient.md#persistqueryclientprovider) 组件包装您的应用程序。

```tsx
import AsyncStorage from '@react-native-async-storage/async-storage'
import { QueryClient } from '@tanstack/react-query'
import { PersistQueryClientProvider } from '@tanstack/react-query-persist-client'
import { createAsyncStoragePersister } from '@tanstack/query-async-storage-persister'

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      cacheTime: 1000 * 60 * 60 * 24, // 24 hours
    },
  },
})

const asyncStoragePersister = createAsyncStoragePersister({
  storage: AsyncStorage
})

const Root = () => (
  <PersistQueryClientProvider
    client={queryClient}
    persistOptions={{ persister: asyncStoragePersister }}
  >
    <App />
  </PersistQueryClientProvider>
);

export default Root;
```

## 重试

重试与 [SyncStoragePersister](../plugins/createSyncStoragePersister) 的工作方式相同，只是它们还可以是异步的。您还可以使用所有预定义的重试处理程序。

## API

### `createAsyncStoragePersister`

调用此函数以创建一个 `asyncStoragePersister`，稍后您可以在 `persistQueryClient` 中使用。

```tsx
createAsyncStoragePersister(options: CreateAsyncStoragePersisterOptions)
```

### `Options`

```tsx
interface CreateAsyncStoragePersisterOptions {
  /** 用于从缓存中设置和检索项目的存储客户端 */
  storage: AsyncStorage | undefined | null
  /** 在将缓存存储到本地存储时使用的键 */
  key?: string
  /** 为避免 localStorage 生成大量数据，
   * 传递一个以毫秒为单位的时间来限制将缓存保存到磁盘 */
  throttleTime?: number
  /** 如何将数据序列化到存储 */
  serialize?: (client: PersistedClient) => string
  /** 如何从存储中反序列化数据 */
  deserialize?: (cachedString: string) => PersistedClient
  /** 如何在出现错误时重试持久化 **/
  retry?: AsyncPersistRetryer
}

interface AsyncStorage {
   getItem: (key: string) => Promise<string | null>
   setItem: (key: string, value: string) => Promise<unknown>
   removeItem: (key: string) => Promise<void>
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
