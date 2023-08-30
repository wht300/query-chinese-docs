---
id: queries
title: 查询（Queries）
ref: docs/react/guides/queries.md
---

[//]: # 'Example'

```ts
import { useQuery } from '@tanstack/vue-query'

const result = useQuery({ queryKey: ['todos'], queryFn: fetchTodoList })
```

[//]: # 'Example'
[//]: # 'Example3'

```vue
<script setup>
import { useQuery } from '@tanstack/vue-query'

const { isLoading, isError, data, error } = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodoList,
})
</script>

<template>
  <span v-if="isLoading">加载中...</span>
  <span v-else-if="isError">错误：{{ error.message }}</span>
  <!-- 到这一步我们可以假设 `isSuccess === true` -->
  <ul v-else-if="data">
    <li v-for="todo in data" :key="todo.id">{{ todo.title }}</li>
  </ul>
</template>
```

[//]: # 'Example3'
[//]: # 'Example4'

```vue
<script setup>
import { useQuery } from '@tanstack/vue-query'

const { status, data, error } = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodoList,
})
</script>

<template>
  <span v-if="status === 'loading'">加载中...</span>
  <span v-else-if="status === 'error'">错误：{{ error.message }}</span>
  <!-- 也可以使用 status === 'success'，但 "else" 的逻辑同样有效 -->
  <ul v-else-if="data">
    <li v-for="todo in data" :key="todo.id">{{ todo.title }}</li>
  </ul>
</template>
```

[//]: # 'Example4'
[//]: # 'Materials'
[//]: # 'Materials'
```
