---
id: disabling-queries
title: 禁用/暂停查询
---

如果您希望禁用自动运行的查询，您可以使用 `enabled = false` 选项。

当 `enabled` 为 `false` 时：

- 如果查询具有缓存数据，那么查询将在 `status === 'success'` 或 `isSuccess` 状态下初始化。
- 如果查询没有缓存数据，那么查询将从 `status === 'loading'` 和 `fetchStatus === 'idle'` 状态开始。
- 查询将不会在挂载时自动获取数据。
- 查询将不会自动在后台进行重新获取。
- 查询将忽略查询客户端的 `invalidateQueries` 和 `refetchQueries` 调用，这些调用通常会导致查询重新获取。
- `useQuery` 返回的 `refetch` 可用于手动触发查询获取数据。

[//]: # 'Example'

```tsx
function Todos() {
  const { isInitialLoading, isError, data, error, refetch, isFetching } =
    useQuery({
      queryKey: ['todos'],
      queryFn: fetchTodoList,
      enabled: false,
    })

  return (
    <div>
      <button onClick={() => refetch()}>获取待办事项</button>

      {data ? (
        <>
          <ul>
            {data.map((todo) => (
              <li key={todo.id}>{todo.title}</li>
            ))}
          </ul>
        </>
      ) : isError ? (
        <span>错误：{error.message}</span>
      ) : isInitialLoading ? (
        <span>加载中...</span>
      ) : (
        <span>尚未准备好...</span>
      )}

      <div>{isFetching ? '获取中...' : null}</div>
    </div>
  )
}
```

[//]: # 'Example'

永久禁用查询会放弃 TanStack Query 提供的许多优秀功能（例如后台重新获取），而且这也不是一种惯用的方法。它从声明式的方式（在查询应何时运行时定义依赖项）转变为命令式模式（我在这里点击时获取）。也不可能向 `refetch` 传递参数。通常情况下，您只需要一个延迟查询来推迟初始获取：

## 延迟查询

`enabled` 选项不仅可以用于永久禁用查询，还可以在以后的某个时间启用/禁用查询。一个很好的例子是筛选表单，您只想在用户输入筛选值后才触发第一次请求：

[//]: # 'Example2'

```tsx
function Todos() {
  const [filter, setFilter] = React.useState('')

  const { data } = useQuery({
      queryKey: ['todos', filter],
      queryFn: () => fetchTodos(filter),
      // ⬇️ 仅在筛选器不为空时启用
      enabled: !!filter
  })

  return (
      <div>
        // 🚀 应用筛选器将启用并执行查询
        <FiltersForm onApply={setFilter} />
        {data && <TodosTable data={data}} />
      </div>
  )
}
```

[//]: # 'Example2'

### isInitialLoading

延迟查询从一开始就处于 `status: 'loading'` 状态，因为 `loading` 意味着尚无数据。从技术上讲，这是正确的，然而，由于我们当前并未获取任何数据（因为查询未 _启用_），因此您可能无法使用此标志显示加载中的旋转图标。

如果您正在使用禁用或延迟查询，您可以使用 `isInitialLoading` 标志。它是一个派生标志，其计算方式为：

`isLoading && isFetching`

因此，只有在查询首次获取数据时，它才会为真。
