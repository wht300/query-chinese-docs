---
id: placeholder-query-data
title: 占位符查询数据
ref: docs/react/guides/placeholder-query-data.md
---

[//]: # 'Example'

```tsx
const result = useQuery({
  queryKey: ['todos'],
  queryFn: () => fetch('/todos'),
  placeholderData: placeholderTodos,
})
```

[//]: # 'Example'
[//]: # 'Memoization'
[//]: # 'Memoization'
[//]: # 'Example2'
[//]: # 'Example2'
[//]: # 'Example3'

```tsx
const result = useQuery({
  queryKey: ['blogPost', blogPostId],
  queryFn: () => fetch(`/blogPosts/${blogPostId}`),
  placeholderData: () => {
    // 使用 'blogPosts' 查询中较小/预览版本的 blogPost 作为此 blogPost 查询的占位符数据
    return queryClient
      .getQueryData(['blogPosts'])
      ?.find((d) => d.id === blogPostId)
  },
})
```

[//]: # 'Example3'
[//]: # 'Materials'
[//]: # 'Materials'
```
