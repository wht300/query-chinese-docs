---
id: important-defaults
title: 重要默认设置

TanStack Query 在初始情况下配置了**积极但合理**的默认值。**有时这些默认值可能会让新用户感到困惑，或者如果用户不知道这些默认值，可能会使学习和调试变得困难。** 当您继续学习和使用 TanStack Query 时，请牢记这些默认值：

- 通过 `useQuery` 或 `useInfiniteQuery` 获取的查询实例默认**将缓存的数据视为陈旧**。

> 要更改此行为，可以使用 `staleTime` 选项在全局和每个查询上进行配置。指定较长的 `staleTime` 表示查询不会频繁地重新获取数据。

- 在以下情况下，陈旧的查询会在后台自动重新获取：
- 查询的新实例挂载
- 窗口重新获得焦点
- 网络重新连接
- 查询选择性地配置了重新获取间隔

如果您看到一个意外的重新获取，很可能是因为您刚刚将窗口聚焦，而 TanStack Query 正在执行[`refetchOnWindowFocus`](../guides/window-focus-refetching)。在开发过程中，这可能会更频繁地触发，特别是因为在浏览器开发工具和您的应用程序之间切换焦点也会导致获取，所以请注意。

> 要更改此功能，您可以使用 `refetchOnMount`、`refetchOnWindowFocus`、`refetchOnReconnect` 和 `refetchInterval` 等选项。

- 查询结果如果没有更多的 `useQuery`、`useInfiniteQuery` 实例或查询观察者，将被标记为"非活动"，并在缓存中保留，以防将来再次使用。

- 默认情况下，"非活动" 查询在**5分钟**后被垃圾回收。

> 要更改这个设置，您可以将默认的查询 `cacheTime` 改为不是 `1000 * 60 * 5` 毫秒的其他值。

- 默认情况下，查询失败会进行**静默重试 3 次，具有指数回退延迟**，然后才会捕获并显示错误给 UI。

> 要更改此设置，您可以将查询的默认 `retry` 和 `retryDelay` 选项改为 `3` 和默认的指数回退函数以外的其他值。

- 默认情况下，查询结果**通过结构共享来检测数据是否实际改变**，如果没有改变，**数据引用保持不变**，以更好地支持 `useMemo` 和 `useCallback` 的值稳定性。如果这个概念听起来很陌生，那就不必担心！在99.9%的情况下，您不需要禁用它，它可以在没有成本的情况下提高您的应用性能。

> 结构共享仅适用于与 JSON 兼容的值，任何其他值类型都将始终被视为已更改。如果由于大型响应等原因导致性能问题，您可以通过 `config.structuralSharing` 标志禁用此功能。如果您在查询响应中处理非 JSON 兼容值，并且仍然想检测数据是否已更改，您可以使用 `config.isDataEqual` 定义数据比较函数，或者将自定义函数作为 `config.structuralSharing` 提供，以从旧响应和新响应中计算一个值，根据需要保留引用。

## 进一步阅读

请查阅以下社区资源中的文章，以获取有关这些默认值的进一步解释：

- [实用的 React Query](../community/tkdodos-blog#1-practical-react-query)
- [将 React Query 作为状态管理器](../community/tkdodos-blog#10-react-query-as-a-state-manager)
