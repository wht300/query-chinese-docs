---
id: does-this-replace-client-state
title: TanStack Query 是否替代 Redux、MobX 或其他全局状态管理器？
---

好的，让我们从一些重要的要点开始：

- TanStack Query 是一个 **服务器状态** 库，负责管理服务器和客户端之间的异步操作。
- Redux、MobX、Zustand 等是 **客户端状态** 库，虽然也可以用于存储异步数据，但与 TanStack Query 相比效率较低。

在考虑了这些要点之后，简短的回答是，TanStack Query **取代了在客户端状态下管理缓存数据的样板代码和相关连接代码，用几行代码就能实现相同功能。**

对于绝大多数应用程序来说，将所有异步代码迁移到 TanStack Query 后，真正剩下的 **全局可访问的客户端状态** 通常非常小。

> 当然，仍然存在一些情况，某些应用程序可能确实有大量的同步客户端状态（比如视觉设计或音乐制作应用程序），在这种情况下，您可能仍然需要一个客户端状态管理器。在这种情况下，重要的是要注意，**TanStack Query 并不替代本地/客户端状态管理**。但是，您可以将 TanStack Query 与大多数客户端状态管理器一起使用，而不会出现任何问题。

## 一个假设的例子

下面我们有一些由全局状态库管理的“全局”状态：

```tsx
const globalState = {
  projects,
  teams,
  tasks,
  users,
  themeMode,
  sidebarStatus,
}
```

目前，全局状态管理器正在缓存 4 种类型的服务器状态：`projects`、`teams`、`tasks` 和 `users`。如果我们将这些服务器状态资源移到 TanStack Query，那么我们剩下的全局状态将会更像这样：

```tsx
const globalState = {
  themeMode,
  sidebarStatus,
}
```

这也意味着，通过几次 `useQuery` 和 `useMutation` 的 hook 调用，我们还可以去除用于管理服务器状态的任何样板代码，例如：

- 连接器（Connectors）
- 动作创建器（Action Creators）
- 中间件（Middlewares）
- Reducer
- 加载/错误/结果状态
- 上下文（Contexts）

当所有这些东西都被移除后，你可能会问自己，“是否值得继续使用客户端状态管理器来处理这个微小的全局状态？”

**这取决于你！**

但是 TanStack Query 的角色是清晰的。它从应用程序中去除了异步连接和样板代码，并用几行代码替代了它们。

你还在等什么，赶快试一试吧！
