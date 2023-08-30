---
id: query-keys
title: 查询键（Query Keys）
---

在其核心中，TanStack Query 基于查询键为您管理查询缓存。查询键必须是顶层的数组，可以简单地是一个只包含一个字符串的数组，也可以是一个由许多字符串和嵌套对象组成的数组。只要查询键是可序列化的，并且**唯一标识了查询的数据**，您就可以使用它！

## 简单的查询键

最简单的键形式是一个包含常量值的数组。这种格式对以下情况有用：

- 通用的列表/索引资源
- 非分层资源

[//]: # 'Example'

```tsx
// 一个代表待办事项列表的查询键
useQuery({ queryKey: ['todos'], ... })

// 其他任何内容！
useQuery({ queryKey: ['something', 'special'], ... })
```

[//]: # 'Example'

## 带有变量的数组键

当一个查询需要更多信息来唯一描述其数据时，您可以使用一个包含字符串和任意数量可序列化对象的数组来描述它。这在以下情况下非常有用：

- 分层或嵌套资源
- 通常会传递一个 ID、索引或其他原始值来唯一标识项目
- 带有附加参数的查询
- 通常会传递一个包含附加选项的对象

[//]: # 'Example2'

```tsx
// 一个代表单个待办事项的查询键
useQuery({ queryKey: ['todo', 5], ... })

// 一个以“预览”格式呈现的单个待办事项的查询键
useQuery({ queryKey: ['todo', 5, { preview: true }], ...})

// 一个代表“完成”的待办事项列表的查询键
useQuery({ queryKey: ['todos', { type: 'done' }], ... })
```

[//]: # 'Example2'

## 查询键以确定性方式进行哈希！

这意味着无论对象中的键的顺序如何，以下所有查询都被认为是相等的：

[//]: # 'Example3'

```tsx
useQuery({ queryKey: ['todos', { status, page }], ... })
useQuery({ queryKey: ['todos', { page, status }], ...})
useQuery({ queryKey: ['todos', { page, status, other: undefined }], ... })
```

[//]: # 'Example3'

然而，以下查询键是不相等的。数组项的顺序很重要！

[//]: # 'Example4'

```tsx
useQuery({ queryKey: ['todos', status, page], ... })
useQuery({ queryKey: ['todos', page, status], ...})
useQuery({ queryKey: ['todos', undefined, page, status], ...})
```

[//]: # 'Example4'

## 如果您的查询函数依赖于变量，请将其包含在查询键中

由于查询键唯一地描述了它们正在获取的数据，因此它们应该包含您在查询函数中使用的**变化**的任何变量。例如：

[//]: # 'Example5'

```tsx
function Todos({ todoId }) {
const result = useQuery({
queryKey: ['todos', todoId],
queryFn: () => fetchTodoById(todoId),
})
}
```

[//]: # 'Example5'

请注意，查询键充当查询函数的依赖项。将依赖变量添加到查询键中将确保独立地缓存查询，并且每当变量发生更改时，*查询将自动重新获取*（取决于您的 `staleTime` 设置）。有关更多信息和示例，请参阅 [exhaustive-deps](../eslint/exhaustive-deps) 部分。

[//]: # 'Materials'

## 进一步阅读

有关在较大的应用程序中组织查询键的提示，请查看 [Effective React Query Keys](../community/tkdodos-blog#8-effective-react-query-keys)，并检查来自社区资源的 [Query Key Factory Package](../community/lukemorales-query-key-factory)。

[//]: # 'Materials'
