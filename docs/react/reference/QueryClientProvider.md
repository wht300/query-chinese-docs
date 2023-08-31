---
id: QueryClientProvider
title: 查询客户端提供者
---

使用 `QueryClientProvider` 组件来连接并向你的应用程序提供一个 `QueryClient`：

```tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'

const queryClient = new QueryClient()

function App() {
  return <QueryClientProvider client={queryClient}>...</QueryClientProvider>
}
```

**选项**

- `client: QueryClient`
  - **必填**
  - 要提供的 QueryClient 实例
- `contextSharing: boolean`
  - **已弃用**
  - 默认为 `false`
  - 将其设置为 `true` 以启用上下文共享，这将在整个窗口中共享上下文的第一个和至少一个实例，以确保如果在不同的捆绑包或微前端中使用 React Query，则所有捆绑包或微前端都将使用同一个上下下文的**实例**，而不考虑模块范围。
- `context?: React.Context<QueryClient | undefined>`
  - 使用此选项使用自定义的 React Query 上下文。否则，将使用 `defaultContext`。
