---
id: query-retries
title: 查询重试 - Query Retries
ref: docs/react/guides/query-retries.md
replace: { 'Provider': 'Plugin' }
---

[//]: # 'Example'

```tsx
import { useQuery } from '@tanstack/vue-query'

// Make a specific query retry a certain number of times
// 使特定查询在失败后重新尝试多次
const result = useQuery({
  queryKey: ['todos', 1],
  queryFn: fetchTodoListPage,
  // Will retry failed requests 10 times before displaying an error
  // 在显示错误之前，将失败的请求重试10次
  retry: 10, 
})
```

[//]: # 'Example'
[//]: # 'Example2'

```ts
import { VueQueryPlugin } from '@tanstack/vue-query'

const vueQueryPluginOptions = {
  queryClientConfig: {
    defaultOptions: {
      queries: {
        retryDelay: (attemptIndex) => Math.min(1000 * 2 ** attemptIndex, 30000),
      },
    },
  },
}
app.use(VueQueryPlugin, vueQueryPluginOptions)
```

[//]: # 'Example2'
