---
id: tkdodos-blog
title: TkDodo 的博客文章合集
---

TanStack Query 的维护者 [TkDodo](https://twitter.com/tkdodo) 撰写了一系列关于使用和使用该库的博客文章。其中一些文章展示了通用的最佳实践，但大多数都有一个 _主观_ 的观点。

## [#1: 实用的 React Query](https://tkdodo.eu/blog/practical-react-query)

> 一篇高级介绍 React Query 的文章，展示了超越文档的实用技巧。它涵盖了解释默认值（`staleTime` vs. `cacheTime`）的概念，例如将服务器状态和客户端状态分开，处理依赖关系和创建自定义钩子，以及概述为什么 `enabled` 选项非常强大。[阅读更多...](https://tkdodo.eu/blog/practical-react-query)

## [#2: React Query 数据转换](https://tkdodo.eu/blog/react-query-data-transformations)

> 了解使用 React Query 执行常见且重要的数据转换任务的可能性。从在 `queryFn` 中进行转换到使用 `select` 选项，本文概述了所有不同方法的利弊。[阅读更多...](https://tkdodo.eu/blog/react-query-data-transformations)

## [#3: React Query 渲染优化](https://tkdodo.eu/blog/react-query-render-optimizations)

> 让我们看看当使用 React Query 时，如果组件重新渲染得太频繁，可以做些什么。该库已经非常优化，但仍然有一些可以选择的功能（例如 `tracked queries`），可以用来避免 `isFetching` 过渡。我们还将探讨 `structural sharing` 指的是什么。[阅读更多...](https://tkdodo.eu/blog/react-query-render-optimizations)

## [#4: React Query 状态检查](https://tkdodo.eu/blog/status-checks-in-react-query)

> 我们通常先检查 `isLoading`，然后再检查 `isError`，但有时，首先检查 `data` 是否可用是应该做的第一件事。本文展示了错误的状态检查顺序如何对用户体验产生负面影响。[阅读更多...](https://tkdodo.eu/blog/status-checks-in-react-query)

## [#5: 测试 React Query](https://tkdodo.eu/blog/testing-react-query)

> 文档已经很好地介绍了测试 React Query 的入门步骤。本文展示了一些额外的技巧（例如关闭 `retries` 或禁用 `console` 输出），当测试使用它们的自定义钩子或组件时，您可能希望遵循这些技巧。它还链接到了一个包含使用 `mock-service-worker` 驱动的成功和错误状态测试的 [示例存储库](https://github.com/TkDodo/testing-react-query)。[阅读更多...](https://tkdodo.eu/blog/testing-react-query)

## [#6: React Query 和 TypeScript](https://tkdodo.eu/blog/react-query-and-type-script)

> 由于 React Query 是用 TypeScript 编写的，它对 TypeScript 有很好的支持。本文解释了各种泛型，以及如何利用类型推断来避免必须显式为 `useQuery` 等内容进行类型化，如何处理 `unknown` 错误，类型缩小如何工作等等！[阅读更多...](https://tkdodo.eu/blog/react-query-and-type-script)

## [#7: 使用 React Query 进行 WebSocket](https://tkdodo.eu/blog/using-web-sockets-with-react-query)

> 一个逐步指南，介绍如何使用 React Query 实现实时通知，无论是基于事件的订阅还是直接将完整数据推送到客户端。适用于从浏览器本机 WebSocket API 到 Firebase，甚至是 GraphQL 订阅。[阅读更多...](https://tkdodo.eu/blog/using-web-sockets-with-react-query)

## [#8: 有效的 React Query 键](https://tkdodo.eu/blog/effective-react-query-keys)

> 大多数示例只是使用简单的字符串或数组查询键，但是一旦应用程序的规模超过待办事项列表，你应该如何有效地组织键呢？本文展示了如何使用协同定位和查询键工厂可以使生活变得更简单。[阅读更多...](https://tkdodo.eu/blog/effective-react-query-keys)

## [#8a: 利用查询函数上下文](https://tkdodo.eu/blog/leveraging-the-query-function-context)

> 在前一篇博客的修正中，我们将看看如何利用查询函数上下文和对象查询键，以在应用程序增长时实现最大的安全性。[阅读更多...](https://tkdodo.eu/blog/leveraging-the-query-function-context)

## [#9: 在 React Query 中使用占位符和初始数据](https://tkdodo.eu/blog/placeholder-and-initial-data-in-react-query)

> 占位符和初始数据是两个类似但不同的概念，用于在同步显示数据时，代替加载旋转器以提高应用程序的用户体验。本文比较了这两者，并概述了每个场景的优点。[阅读更多...](https://tkdodo.eu/blog/placeholder-and-initial-data-in-react-query)

## [#10: 将 React Query 用作状态管理器](https://tkdodo.eu/blog/react-query-as-a-state-manager)

> React Query 不会为您获取任何数据 - 它是一个在用于服务器状态时表现出色的数据同步工具。本文涵盖了使 React Query 成为异步状态的单一数据源的所有需要知道的内容。您将学习如何让 React Query 发挥其魔力，以及

为什么定制 `staleTime` 可能是您所需的全部。[阅读更多...](https://tkdodo.eu/blog/react-query-as-a-state-manager)

## [#11: 处理 React Query 错误](https://tkdodo.eu/blog/react-query-error-handling)

> 处理错误是处理异步数据的一个重要部分，尤其是数据获取。我们必须面对这一点：并非所有请求都会成功，也不是所有 Promise 都会被实现。本文介绍了处理 React Query 中错误的各种方式，如错误属性、使用错误边界或 onError 回调，以便您可以为应用程序做好 "出现问题" 的情况准备。[阅读更多...](https://tkdodo.eu/blog/react-query-error-handling)

## [#12: 精通 React Query 中的 Mutations](https://tkdodo.eu/blog/mastering-mutations-in-react-query)

> 在处理服务器数据时，Mutation 是必不可少的第二部分 - 用于需要更新数据的情况。本文介绍了 Mutation 是什么，以及它们与查询的区别。您将了解到 `mutate` 和 `mutateAsync` 之间的区别，以及如何将查询和 Mutation 绑定在一起。[阅读更多...](https://tkdodo.eu/blog/mastering-mutations-in-react-query)

## [#13: 离线的 React Query](https://tkdodo.eu/blog/offline-react-query)

> 有很多方法可以产生 promises - 这是 React Query 需要的一切 - 但迄今为止，最大的用例是数据获取。通常，这需要一个活动的网络连接。但有时，在网络连接不稳定的移动设备上，您的应用程序也需要在没有网络的情况下工作。在本文中，您将了解到 React Query 提供的不同离线策略。[阅读更多...](https://tkdodo.eu/blog/offline-react-query)

## [#14: React Query 与表单](https://tkdodo.eu/blog/react-query-and-forms)

> 表单往往模糊了服务器状态和客户端状态之间的界限。在大多数应用程序中，我们不仅希望显示状态，还希望让用户与之交互。本文展示了两种不同的方法，以及有关在表单中使用 React Query 的一些技巧。[阅读更多...](https://tkdodo.eu/blog/react-query-and-forms)

## [#15: React Query 常见问题解答](https://tkdodo.eu/blog/react-query-fa-qs)

> 本文试图回答关于 React Query 最常见的问题。[阅读更多...](https://tkdodo.eu/blog/react-query-fa-qs)

## [#16: React Query 遇上 React Router](https://tkdodo.eu/blog/react-query-meets-react-router)

> Remix 和 React Router 在考虑何时获取数据时正在改变游戏规则。本文将介绍为什么 React Query 和支持数据加载的路由器是天作之合。[阅读更多...](https://tkdodo.eu/blog/react-query-meets-react-router)

## [#17: 种子查询缓存](https://tkdodo.eu/blog/seeding-the-query-cache)

> 本博客文章展示了多种在开始渲染之前将数据放入查询缓存中的方法，以最小化应用程序中显示的加载旋转器的数量。这些选项范围从在服务器上或在路由器中进行预取，到通过 `setQueryData` 对缓存条目进行种子。[阅读更多...](https://tkdodo.eu/blog/seeding-the-query-cache)

## [#18: 深入了解 React Query](https://tkdodo.eu/blog/inside-react-query)

> 如果您曾经想知道 React Query 在底层是如何工作的 - 这篇文章适合您。它解释了架构（包括可视化），从通用的查询核心开始，以及它如何与特定于框架的适配器进行通信。[阅读更多...](https://tkdodo.eu/blog/inside-react-query)

## [#19: 类型安全的 React Query](https://tkdodo.eu/blog/type-safe-react-query)

> "拥有类型" 与 "类型安全" 之间存在着很大的区别。本文试图概述这些差异，并展示在使用 TypeScript 与 React Query 一起使用时，如何获得最佳的类型安全性。[阅读更多...](https://tkdodo.eu/blog/type-safe-react-query)
