---
id: query-retries
title: 查询重试（Query Retries）
---

当 `useQuery` 查询失败时（查询函数抛出错误），TanStack Query 会自动重试查询，前提是该查询的请求未达到连续重试的最大次数（默认为 `3`），或者提供了一个函数来确定是否允许重试。

您可以在全局级别和单个查询级别上配置重试。

- 将 `retry = false` 设置将禁用重试。
- 将 `retry = 6` 设置将在显示函数抛出的最终错误之前，重试失败的请求 6 次。
- 将 `retry = true` 设置将无限次地重试失败的请求。
- 将 `retry = (failureCount, error) => ...` 设置允许基于请求失败的原因进行自定义逻辑。

[//]: # 'Example'

```tsx
import { useQuery } from '@tanstack/react-query'

// 使特定查询在失败时重试特定次数
const result = useQuery({
queryKey: ['todos', 1],
queryFn: fetchTodoListPage,
retry: 10, // 在显示错误之前将重试失败的请求 10 次
})
```

[//]: # 'Example'

## 重试延迟（Retry Delay）

默认情况下，TanStack Query 中的重试不会在请求失败后立即发生。与标准情况一样，逐渐应用退避延迟到每次重试尝试。

默认的 `retryDelay` 设置为双倍（从 `1000`ms 开始），每次尝试增加，但不会超过 30 秒：

[//]: # 'Example2'

```tsx
// 为所有查询配置
import {
QueryCache,
QueryClient,
QueryClientProvider,
} from '@tanstack/react-query'

const queryClient = new QueryClient({
defaultOptions: {
queries: {
retryDelay: (attemptIndex) => Math.min(1000 * 2 ** attemptIndex, 30000),
},
},
})

function App() {
return <QueryClientProvider client={queryClient}>...</QueryClientProvider>
}
```

[//]: # 'Example2'

尽管并不推荐，但您可以在提供程序和单个查询选项中覆盖 `retryDelay` 函数/整数。如果设置为整数而不是函数，则延迟将始终是相同的时间量：

[//]: # 'Example3'

```tsx
const result = useQuery({
queryKey: ['todos'],
queryFn: fetchTodoList,
retryDelay: 1000, // 无论重试多少次，都将等待 1000ms 后重试
})
```

[//]: # 'Example3'
