---
id: exhaustive-deps
title: 完整依赖项的查询键
---

查询键应该被视为查询函数的依赖数组：每个在查询函数内部使用的变量都应该添加到查询键中。
这可以确保查询被独立地缓存，并且在变量更改时自动重新获取查询。

## 规则详解

以下是此规则的错误示例代码：

```tsx
/* eslint "@tanstack/query/exhaustive-deps": "error" */

useQuery({
    queryKey: ["todo"],
    queryFn: () => api.getTodo(todoId)
})

const todoQueries = {
    detail: (id) => ({ queryKey: ["todo"], queryFn: () => api.getTodo(id) })
}
```

以下是此规则的正确示例代码：

```tsx
useQuery({
    queryKey: ["todo", todoId],
    queryFn: () => api.getTodo(todoId)
})

const todoQueries = {
    detail: (id) => ({ queryKey: ["todo", id], queryFn: () => api.getTodo(id) })
}
```

## 何时不使用

如果您不关心查询键的规则，则不需要使用此规则。

## 属性

- [x] ✅ 推荐
- [x] 🔧 可修复
