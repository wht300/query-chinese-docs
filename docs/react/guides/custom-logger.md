---
id: custom-logger
title: 自定义日志记录器
---

如果您想要更改 TanStack Query 记录信息的方式，您可以在创建 `QueryClient` 时设置自定义日志记录器。

```tsx
const queryClient = new QueryClient({
  logger: {
    log: (...args) => {
      // 记录调试信息
    },
    warn: (...args) => {
      // 记录警告
    },
    error: (...args) => {
      // 记录错误
    },
  },
})
```

**已弃用**

自定义日志记录器已经被弃用，并将在下一个主要版本中被移除。
日志记录仅在开发模式下生效，在那里传递自定义日志记录器是不必要的。
