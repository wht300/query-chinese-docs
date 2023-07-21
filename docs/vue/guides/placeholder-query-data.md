---
id: placeholder-query-data
title: 查询数据占位符 - Placeholder Query Data
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
    // Use the smaller/preview version of the blogPost from the 'blogPosts'
    // query as the placeholder data for this blogPost query
    // 将会从 'blogPosts' 查询到的数据中找到该查询id的数据作为placeholder数据显示
    return queryClient
      .getQueryData(['blogPosts'])
      ?.find((d) => d.id === blogPostId)
  },
})
```

[//]: # 'Example3'
[//]: # 'Materials'
[//]: # 'Materials'
