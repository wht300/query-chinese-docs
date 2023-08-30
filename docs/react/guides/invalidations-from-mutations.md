---
id: invalidations-from-mutations
title: 由突变引起的失效
---

失效查询只是战斗的一半。知道**何时**使其失效才是另一半。通常在应用程序中的突变成功时，非常有可能有相关的查询需要失效，并且可能需要重新获取，以便考虑突变带来的新更改。

例如，假设我们有一个发布新待办事项的突变：

[//]: # '示例'

```tsx
const mutation = useMutation({ mutationFn: postTodo })
```

[//]: # '示例'

当成功发生`postTodo`突变时，我们很可能希要失效所有`todos`查询，并可能需要重新获取以显示新的待办事项。为此，您可以使用`useMutation`的`onSuccess`选项以及`client`的`invalidateQueries`函数：

[//]: # '示例2'

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query'

const queryClient = useQueryClient()

// 当此突变成功时，使带有`todos`或`reminders`查询键的任何查询失效
const mutation = useMutation({
  mutationFn: addTodo,
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['todos'] })
    queryClient.invalidateQueries({ queryKey: ['reminders'] })
  },
})
```

[//]: # '示例2'
You can wire up your invalidations to happen using any of the callbacks available in the [`useMutation` hook](../guides/mutations)
您可以通过[`useMutation`hook](../guides/mutations)中提供的任何回调之一来使失效发生。
