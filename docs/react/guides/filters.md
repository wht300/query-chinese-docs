---
id: filters
title: 过滤器
---

TanStack Query 中的某些方法接受 `QueryFilters` 或 `MutationFilters` 对象。

## `查询过滤器`

查询过滤器是一个具有特定匹配条件的对象，用于匹配查询：

```tsx
// 取消所有查询
await queryClient.cancelQueries()

// 移除所有在键中以 `posts` 开头的非活动查询
queryClient.removeQueries({ queryKey: ['posts'], type: 'inactive' })

// 重新获取所有活动查询
await queryClient.refetchQueries({ type: 'active' })

// 重新获取所有在键中以 `posts` 开头的活动查询
await queryClient.refetchQueries({ queryKey: ['posts'], type: 'active' })
```

查询过滤器对象支持以下属性：

- `queryKey?: QueryKey`
  - 设置此属性以定义要匹配的查询键。
- `exact?: boolean`
  - 如果不想通过查询键进行包含性搜索，可以传递 `exact: true` 选项，仅返回与您传递的完全相同的查询。
- `type?: 'active' | 'inactive' | 'all'`
  - 默认为 `all`
  - 当设置为 `active` 时，将匹配活动查询。
  - 当设置为 `inactive` 时，将匹配非活动查询。
- `stale?: boolean`
  - 当设置为 `true` 时，将匹配陈旧的查询。
  - 当设置为 `false` 时，将匹配新鲜的查询。
- `fetchStatus?: FetchStatus`
  - 当设置为 `fetching` 时，将匹配当前正在获取数据的查询。
  - 当设置为 `paused` 时，将匹配因为被 `paused` 而停止获取数据的查询。
  - 当设置为 `idle` 时，将匹配未在获取数据的查询。
- `predicate?: (query: Query) => boolean`
  - 此谓词函数将用作对所有匹配的查询的最终过滤器。如果没有指定其他过滤器，则此函数将针对缓存中的每个查询进行评估。

## `突变过滤器`

突变过滤器是一个具有特定匹配条件的对象，用于匹配突变：

```tsx
// 获取所有正在获取的突变数
await queryClient.isMutating()

// 通过突变键过滤突变
await queryClient.isMutating({ mutationKey: ["post"] })

// 使用谓词函数过滤突变
await queryClient.isMutating({
  predicate: (mutation) => mutation.options.variables?.id === 1,
})
```

突变过滤器对象支持以下属性：

- `mutationKey?: MutationKey`
  - 设置此属性以定义要匹配的突变键。
- `exact?: boolean`
  - 如果不想通过突变键进行包含性搜索，可以传递 `exact: true` 选项，仅返回与您传递的完全相同的突变。
- `fetching?: boolean`
  - 当设置为 `true` 时，将匹配当前正在获取数据的突变。
  - 当设置为 `false` 时，将匹配未在获取数据的突变。
- `predicate?: (mutation: Mutation) => boolean`
  - 此谓词函数将用作对所有匹配的突变的最终过滤器。如果没有指定其他过滤器，则此函数将针对缓存中的每个突变进行评估。
