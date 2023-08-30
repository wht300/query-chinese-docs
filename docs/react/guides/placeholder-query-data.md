---
id: placeholder-query-data
title: 占位符查询数据
---

## 什么是占位符数据?

占位符数据使查询可以像已经具有数据一样运行，类似于 `initialData` 选项，但是**数据不会持久保存到缓存中**。在你有足够的部分（或虚假）数据以成功渲染查询的情况下，这在后台获取实际数据时非常有用。

> 示例：单个博客文章查询可以从包含标题和文章摘要的博客文章父列表中提取“预览”数据。您不希望将此部分数据持久保存到个别查询的查询结果中，但在实际查询完成获取整个对象之前，它对于尽快显示内容布局非常有用。

有几种在需要之前为查询提供占位符数据到缓存中的方法：

- 声明式地：
  - 为查询提供 `placeholderData` 以在缓存为空时预填充它
- 命令式地：
  - [使用 `queryClient` 和 `placeholderData` 选项预取或获取数据](../guides/prefetching)

## 占位符数据作为值

[//]: # 'Example'

```tsx
function Todos() {
  const result = useQuery({
    queryKey: ['todos'],
    queryFn: () => fetch('/todos'),
    placeholderData: placeholderTodos,
  })
}
```

[//]: # 'Example'
[//]: # 'Memoization'

### 占位符数据的记忆化

如果访问查询的占位符数据的过程很复杂，或者你不想在每次渲染时执行此操作，你可以对该值进行记忆化：

[//]: # 'Memoization'
[//]: # 'Example2'

```tsx
function Todos() {
  const placeholderData = useMemo(() => generateFakeTodos(), [])
  const result = useQuery({
    queryKey: ['todos'],
    queryFn: () => fetch('/todos'),
    placeholderData,
  })
}
```

[//]: # 'Example2'

### 来自缓存的占位符数据

在某些情况下，您可以从另一个查询的缓存结果中提供查询的占位符数据。一个很好的例子是从博客文章列表查询的缓存数据中搜索预览版本的文章，然后将其用作个别文章查询的占位符数据：

[//]: # 'Example3'

```tsx
function Todo({ blogPostId }) {
  const result = useQuery({
    queryKey: ['blogPost', blogPostId],
    queryFn: () => fetch(`/blogPosts/${blogPostId}`),
    placeholderData: () => {
      // 使用“blogPosts”查询中较小/预览版本的博客文章作为此博客文章查询的占位符数据
      return queryClient
        .getQueryData(['blogPosts'])
        ?.find((d) => d.id === blogPostId)
    },
  })
}
```

[//]: # 'Example3'
[//]: # 'Materials'

## 进一步阅读

有关 `占位符数据` 和 `初始数据` 的比较，请查看 [社区资源](../community/tkdodos-blog#9-placeholder-and-initial-data-in-react-query)。

[//]: # 'Materials'
