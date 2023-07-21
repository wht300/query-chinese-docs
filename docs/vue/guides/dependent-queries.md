---
id: dependent-queries
title: Dependent Queries（依赖查询）
ref: docs/react/guides/dependent-queries.md
---

[//]: # 'Example'

```js
// Get the user
// 获取用户
const { data: user } = useQuery({
  queryKey: ['user', email],
  queryFn: () => getUserByEmail(email.value),
})

const userId = computed(() => user.value?.id)
const enabled = computed(() => !!user.value?.id)

// Then get the user's projects
// 然后获取用户的项目
const { isIdle, data: projects } = useQuery({
  queryKey: ['projects', userId],
  queryFn: () => getProjectsByUser(userId.value),
  // The query will not execute until `enabled == true` 
  // 当 `enabled == true` 时，查询才会执行
  enabled, 
})
```

[//]: # 'Example'
