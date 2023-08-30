---
id: MutationCache
title: 变更缓存 (MutationCache)
---

`MutationCache` 是用于存储变更的地方。

**通常情况下，您不会直接与 `MutationCache` 进行交互，而是使用 `QueryClient`。**

```tsx
import { MutationCache } from '@tanstack/react-query'

const mutationCache = new MutationCache({
  onError: error => {
    console.log(error)
  },
  onSuccess: data => {
    console.log(data)
  },
})
```

其可用方法包括：

- [`getAll`](#mutationcachegetall)
- [`subscribe`](#mutationcachesubscribe)
- [`clear`](#mutationcacheclear)

**选项**

- `onError?: (error: unknown, variables: unknown, context: unknown, mutation: Mutation) => Promise<unknown> | unknown`
  - 可选
  - 当某个变更遇到错误时，将调用此函数。
  - 如果从中返回 Promise，将等待它的完成。
- `onSuccess?: (data: unknown, variables: unknown, context: unknown, mutation: Mutation) => Promise<unknown> | unknown`
  - 可选
  - 当某个变更成功时，将调用此函数。
  - 如果从中返回 Promise，将等待它的完成。
- `onSettled?: (data: unknown | undefined, error: unknown | null, variables: unknown, context: unknown, mutation: Mutation) => Promise<unknown> | unknown`
  - 可选
  - 当某个变更已处理（成功或出错）时，将调用此函数。
  - 如果从中返回 Promise，将等待它的完成。
- `onMutate?: (variables: unknown, mutation: Mutation) => Promise<unknown> | unknown`
  - 可选
  - 在执行某个变更之前，将调用此函数。
  - 如果从中返回 Promise，将等待它的完成。

## 全局回调

`MutationCache` 上的 `onError`、`onSuccess`、`onSettled` 和 `onMutate` 回调可用于在全局级别处理这些事件。它们与提供给 `QueryClient` 的 `defaultOptions` 不同，因为：

- `defaultOptions` 可以被每个变更覆盖，而全局回调将**始终**被调用。
- `onMutate` 不允许返回上下文值。

## `mutationCache.getAll`

`getAll` 返回缓存中的所有变更。

> 注意：对于大多数应用程序来说，通常不需要这个方法，但在需要了解变更的更多信息的罕见情况下可能会有用

```tsx
const mutations = mutationCache.getAll()
```

**返回值**

- `Mutation[]`
  - 来自缓存的变更实例

## `mutationCache.subscribe`

`subscribe` 方法可用于订阅整个变更缓存，并在缓存更新时获得有关变更状态更改、变更被更新、添加或删除的安全/已知更新的通知。

```tsx
const callback = event => {
  console.log(event.type, event.mutation)
}

const unsubscribe = mutationCache.subscribe(callback)
```

**选项**

- `callback: (mutation?: MutationCacheNotifyEvent) => void`
  - 每次更新缓存时，将调用此函数并传递变更缓存。

**返回值**

- `unsubscribe: Function => void`
  - 此函数将取消订阅变更缓存的回调。

## `mutationCache.clear`

`clear` 方法可用于完全清除缓存并重新开始。

```tsx
mutationCache.clear()
```
