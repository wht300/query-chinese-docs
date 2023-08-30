---
id: query-functions
title: 查询函数（Query Functions）
ref: docs/react/guides/query-functions.md
---

[//]: # 'Example4'

```js
const result = useQuery({
  queryKey: ['todos', { status, page }],
  queryFn: fetchTodoList,
})

// 在您的查询函数中访问 key、status 和 page 变量！
function fetchTodoList({ queryKey }) {
  const [_key, { status, page }] = queryKey
  return new Promise()
}
```

[//]: # 'Example4'
```
