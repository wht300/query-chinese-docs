---
id: dependent-queries
title: 依赖查询
---

依赖（或串行）查询在执行之前依赖于先前的查询完成。为了实现这一点，只需使用 `enabled` 选项告诉查询何时准备好运行：

[//]: # 'Example'

```tsx
// 获取用户
const { data: user } = useQuery({
  queryKey: ['user', email],
  queryFn: getUserByEmail,
})

const userId = user?.id

// 然后获取用户的项目
const {
  status,
  fetchStatus,
  data: projects,
} = useQuery({
  queryKey: ['projects', userId],
  queryFn: getProjectsByUser,
  // 只有在 userId 存在时，查询才会执行
  enabled: !!userId,
})
```

[//]: # 'Example'

`projects` 查询将从以下状态开始：

```tsx
status: 'loading'
fetchStatus: 'idle'
```

一旦 `user` 可用，`projects` 查询将被 `enabled`，然后转换为：

```tsx
status: 'loading'
fetchStatus: 'fetching'
```

一旦我们获取了项目，它将转换为：

```tsx
status: 'success'
fetchStatus: 'idle'
```
