 ---
id: caching
title: 缓存示例
---

> 在阅读本指南之前，请仔细阅读[重要的默认值](../guides/important-defaults)

## 基本示例

此缓存示例说明了以下情况的故事和生命周期：

- 带有缓存数据和不带有缓存数据的查询实例
- 后台重新获取
- 不活跃的查询
- 垃圾回收

让我们假设我们正在使用默认的 `cacheTime`（**5分钟**）和默认的 `staleTime`（`0`）。

- 安装新的 `useQuery({ queryKey: ['todos'], queryFn: fetchTodos })` 实例。
  - 由于没有使用 `['todos']` 查询键进行其他查询，此查询将显示硬加载状态并发出网络请求以获取数据。
  - 当网络请求完成时，返回的数据将被缓存在 `['todos']` 键下。
  - 钩子将在配置的 `staleTime`（默认为 `0`，即立即）后将数据标记为过时。
- 在其他地方安装 `useQuery({ queryKey: ['todos'], queryFn: fetchTodos })` 的第二个实例。
  - 由于缓存已经有来自第一个查询的 `['todos']` 键的数据，因此可以立即从缓存中返回该数据。
  - 新的实例使用其查询函数触发新的网络请求。
    - 请注意，无论这两个 `fetchTodos` 查询函数是否相同，这两个查询的 [`status`](../reference/useQuery) 都会更新（包括 `isFetching`、`isLoading` 和其他相关值），因为它们具有相同的查询键。
  - 当请求成功完成时，缓存中的 `['todos']` 键下的数据将使用新数据进行更新，并且两个实例都会使用新数据进行更新。
- 卸载 `useQuery({ queryKey: ['todos'], queryFn: fetchTodos })` 查询的两个实例，不再使用。
  - 由于没有了这个查询的更多活动实例，将使用 `cacheTime` 设置缓存超时来删除并进行垃圾回收（默认为**5分钟**）。
- 在缓存超时完成之前，另一个实例 `useQuery({ queryKey: ['todos'], queryFn: fetchTodos })` 被安装。查询立即返回可用的缓存数据，同时 `fetchTodos` 函数正在后台运行。当它成功完成时，它将使用新数据填充缓存。
- `useQuery({ queryKey: ['todos'], queryFn: fetchTodos })` 的最后一个实例被卸载。
- 在**5分钟**内不再出现 `useQuery({ queryKey: ['todos'], queryFn: fetchTodos })` 的任何实例。
  - 在 `['todos']` 键下的缓存数据将被删除并进行垃圾回收。
