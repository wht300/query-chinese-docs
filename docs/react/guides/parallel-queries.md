---
id: parallel-queries
title: 并行查询
---

“并行”查询是同时执行的查询，以最大程度地增加获取并发性。

## 手动并行查询

当并行查询的数量不变时，**无需额外的努力**就可以使用并行查询。只需将任意数量的 TanStack Query 的 `useQuery` 和 `useInfiniteQuery` 钩子并排使用！

[//]: # 'Example'

```tsx
function App () {
  // 以下查询将并行执行
  const usersQuery = useQuery({ queryKey: ['users'], queryFn: fetchUsers })
  const teamsQuery = useQuery({ queryKey: ['teams'], queryFn: fetchTeams })
  const projectsQuery = useQuery({ queryKey: ['projects'], queryFn: fetchProjects })
  ...
}
```

[//]: # 'Example'
[//]: # 'Info'

> 在使用 React Query 的 suspense 模式时，这种并行性模式不起作用，因为第一个查询将在内部抛出一个 Promise，并在其他查询运行之前暂停组件。要解决这个问题，您可以使用 `useQueries` 钩子（建议使用）或为每个 `useQuery` 实例使用单独的组件来协调自己的并行性（这种方法相对不优雅）。

[//]: # 'Info'

## 使用 `useQueries` 进行动态并行查询

如果从一个渲染到另一个渲染时，您需要执行的查询数量发生变化，那么您不能使用手动查询，因为那将违反钩子的规则。相反，TanStack Query 提供了一个 `useQueries` 钩子，您可以使用它来动态并行执行任意数量的查询。

`useQueries` 接受一个包含 **queries 键** 的 **选项对象**，其值为一个 **查询对象数组**。它返回一个 **查询结果数组**：

[//]: # 'Example2'

```tsx
function App({ users }) {
  const userQueries = useQueries({
    queries: users.map((user) => {
      return {
        queryKey: ['user', user.id],
        queryFn: () => fetchUserById(user.id),
      }
    }),
  })
}
```

[//]: # 'Example2'
