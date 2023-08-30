---
title: Vue Query
---

`vue-query` 包通过 Vue 提供了一个一流的 API，用于使用 TanStack Query。然而，你从这些 hooks 中获得的所有基本原语都是核心 API，它们在所有 TanStack 适配器中共享，包括 Query Client、查询结果、查询订阅等。

## 示例

此示例非常简要地介绍了 Vue Query 的三个核心概念：

- [Queries（查询）](guides/queries)
- [Mutations（变更）](guides/mutations)
- [Query Invalidation（查询失效）](guides/query-invalidation)

```vue
<script setup>
import { useQueryClient, useQuery, useMutation } from "@tanstack/vue-query";

// 访问 QueryClient 实例
const queryClient = useQueryClient();

// 查询
const { isLoading, isError, data, error } = useQuery({ queryKey: ['todos'], queryFn: getTodos });

// 变更
const mutation = useMutation({
  mutationFn: postTodo,
  onSuccess: () => {
    // 使查询失效并重新获取数据
    queryClient.invalidateQueries({ queryKey: ["todos"] });
  },
});

function onButtonClick() {
  mutation.mutate({
    id: Date.now(),
    title: "洗衣服",
  });
}
</script>

<template>
  <span v-if="isLoading">加载中...</span>
  <span v-else-if="isError">错误：{{ error.message }}</span>
  <!-- 到这一步，我们可以假定 `isSuccess === true` -->
  <ul v-else>
    <li v-for="todo in data" :key="todo.id">{{ todo.title }}</li>
  </ul>
  <button @click="onButtonClick">添加任务</button>
</template>
```

这三个概念构成了 Vue Query 的大部分核心功能。文档的下一节将详细介绍每个核心概念。
