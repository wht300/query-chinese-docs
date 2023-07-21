---
id: disabling-queries
title: Disabling/Pausing Queries（禁用/暂停查询）
ref: docs/react/guides/disabling-queries.md
---

[//]: # 'Example'

```vue
<script setup>
import { useQuery } from '@tanstack/vue-query'

const { isInitialLoading, isError, data, error, refetch, isFetching } =
  useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodoList,
    enabled: false,
  })
</script>

<template>
  <button @click="refetch">获取待办事项</button>
  <span v-if="isIdle">尚未准备好...</span>
  <span v-else-if="isError">Error: {{ error.message }}</span>
  <div v-else-if="data">
    <span v-if="isFetching">Fetching...</span>
    <ul>
      <li v-for="todo in data" :key="todo.id">{{ todo.title }}</li>
    </ul>
  </div>
</template>
```

[//]: # 'Example'
[//]: # 'Example2'

```vue
<script setup>
import { useQuery } from '@tanstack/vue-query'

const filter = ref('')
const isEnabled = computed(() => !!filter.value)
const { data } = useQuery({
  queryKey: ['todos', filter],
  queryFn: () => fetchTodos(filter),
  // ⬇️ disabled as long as the filter is empty
  // ⬇️ 仅当 filter 不为空时禁用
  enabled: isEnabled,
})
</script>

<template>
  <span v-if="data">已设置过滤器并且数据已加载！</span>
</template>
```

[//]: # 'Example2'
