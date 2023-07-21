---
id: invalidations-from-mutations
title: 在Mutations时使数据失效 Invalidations from Mutations
ref: docs/react/guides/invalidations-from-mutations.md
---

[//]: # 'Example2'

```tsx
import { useMutation, useQueryClient } from '@tanstack/vue-query'

const queryClient = useQueryClient()

// When this mutation succeeds, invalidate any queries with the `todos` or `reminders` query key
// 当该mutation成功时，将使任何带有 `todos` 或 `reminders` 查询键的查询变为无效
const mutation = useMutation({
  mutationFn: addTodo,
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['todos'] })
    queryClient.invalidateQueries({ queryKey: ['reminders'] })
  },
})
```

[//]: # 'Example2'
