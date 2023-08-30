---
id: background-fetching-indicators
title: 后台获取指示器
ref: docs/react/guides/background-fetching-indicators.md
---

[//]: # 'Example'

```vue
<script setup>
import { useQuery } from '@tanstack/vue-query'

const { isLoading, isFetching, isError, data, error } = useQuery({
  queryKey: ['todos'],
  queryFn: getTodos,
})
</script>

<template>
  <div v-if="isFetching">刷新中...</div>
  <span v-if="isLoading">加载中...</span>
  <span v-else-if="isError">错误：{{ error.message }}</span>
  <!-- 我们可以假定在这一点上 `isSuccess === true` -->
  <ul v-else-if="data">
    <li v-for="todo in data" :key="todo.id">{{ todo.title }}</li>
  </ul>
</template>
```

[//]: # 'Example'
[//]: # 'Example2'

```vue
<script setup>
import { useIsFetching } from '@tanstack/vue-query'

const isFetching = useIsFetching()
</script>

<template>
  <div v-if="isFetching">查询正在后台获取中...</div>
</template>
```

[//]: # 'Example2'
```
