---
id: network-mode
title: 网络模式
---

TanStack Query 提供了三种不同的网络模式，用于区分在没有网络连接的情况下 [查询 (Queries)](../guides/queries) 和 [突变 (Mutations)](../guides/mutations) 应该如何行为。可以为每个查询/突变单独设置此模式，也可以通过查询/突变默认值在全局范围内设置。

由于 TanStack Query 最常与数据获取库一起用于数据获取，因此默认的网络模式是[在线 (online)](#network-mode-online)。

## 网络模式：在线 (online)

在此模式下，只有在有网络连接时，查询和突变才会触发。这是默认模式。如果为查询启动了提取操作，如果无法进行提取操作，因为没有网络连接，它将始终保持在当前的“状态”（`loading`、`error`、`success`）。但是，还会额外公开一个[fetchStatus](../guides/queries#fetchstatus)。它可以是：

- `fetching`：`queryFn` 正在执行 - 请求正在进行中。
- `paused`：查询未执行 - 在重新获得连接之前会被“暂停”
- `idle`：查询既没有获取也没有暂停

`isFetching` 和 `isPaused` 标志是从此状态派生出来的，并为方便起见公开。

> 请记住，仅检查“加载”状态可能不足以显示加载旋转器。查询可能处于`state: 'loading'`状态，但如果它们是首次加载，且没有网络连接，则`fetchStatus: 'paused'`。

如果查询在您在线时运行，但在提取仍在进行时断开连接，TanStack Query 也会暂停重试机制。暂停的查询将在您重新获得网络连接后继续运行。这与`refetchOnReconnect`无关（在此模式下也默认为`true`），因为它不是“重新提取”，而是“继续”。如果查询在此期间已被[取消](../guides/query-cancellation)，它将不会继续运行。

## 网络模式：始终 (always)

在此模式下，TanStack Query 将始终提取并忽略在线/离线状态。如果您在不需要活动网络连接即可正常工作的环境中使用 TanStack Query，这可能是您要选择的模式 - 例如，如果您只是从`AsyncStorage`中读取，或者如果您只想从`queryFn`返回`Promise.resolve(5)`。

- 查询永远不会“暂停”，因为您没有网络连接。
- 重试也不会暂停 - 如果查询失败，它将转为“error”状态。
- 在此模式中，默认为`false`，因为重新连接到网络不再是陈旧查询应该重新提取的良好指标。如果需要，您仍然可以打开它。

## 网络模式：离线优先 (offlineFirst)

此模式是前两个选项之间的折中方案，TanStack Query 将运行`queryFn`一次，然后暂停重试。如果您的服务工作器拦截请求以进行缓存（例如在[离线优先的 PWA](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Offline_Service_workers)中），或者如果您通过[Cache-Control header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching#the_cache-control_header)使用HTTP缓存，这将非常方便。

在这些情况下，第一次提取可能会成功，因为它来自离线存储/缓存。但是，如果缓存未命中，则网络请求将发出并失败，在这种情况下，此模式的行为类似于“在线”查询 - 暂停重试。

## 开发工具 (Devtools)

[TanStack Query 开发工具](../devtools)将显示查询处于“暂停”状态，如果查询将进行提取，但没有网络连接。还有一个切换按钮可以用于“模拟离线行为”。请注意，此按钮实际上不会干扰您的网络连接（您可以在浏览器开发工具中执行此操作），但它会将[OnlineManager](../reference/onlineManager)设置为离线状态。

## 签名 (Signature)

- `networkMode: 'online' | 'always' | 'offlineFirst'`
  - 可选项
  - 默认为`'online'`
