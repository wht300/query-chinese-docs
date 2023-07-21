---
id: quick-start
title: 快速入门 - Quick Start
ref: docs/react/quick-start.md
replace: { 'React': 'Vue', 'react-query': 'vue-query' }
---

[//]: # 'Example'

If you're looking for a fully functioning example, please have a look at our [basic codesandbox example](../examples/vue/basic)

如果您正在寻找一个完全功能的示例，请查看我们的[basic codesandbox 示例](../examples/vue/basic)
```vue
<script setup>
import { useQueryClient, useQuery, useMutation } from '@tanstack/vue-query'

// Access QueryClient instance
// 获取 QueryClient 实例
const queryClient = useQueryClient()

// 查询 - Query
const { isLoading, isError, data, error } = useQuery({
  queryKey: ['todos'],
  queryFn: getTodos,
})

// 突变 - Mutation
const mutation = useMutation({
  mutationFn: postTodo,
  onSuccess: () => {
    // Invalidate and refetch
    // 置为失效并重新获取
    queryClient.invalidateQueries({ queryKey: ['todos'] })
  },
})

function onButtonClick() {
  mutation.mutate({
    id: Date.now(),
    title: 'Do Laundry',
  })
}
</script>

<template>
  <span v-if="isLoading">Loading...</span>
  <span v-else-if="isError">Error: {{ error.message }}</span>
  <!-- We can assume by this point that `isSuccess === true` -->
  <!-- 我们可以假设此时 `isSuccess === true` -->
  <ul v-else>
    <li v-for="todo in data" :key="todo.id">{{ todo.title }}</li>
  </ul>
  <button @click="onButtonClick">Add Todo</button>
</template>
```

[//]: # 'Example'

