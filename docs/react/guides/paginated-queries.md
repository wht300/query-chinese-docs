---
id: paginated-queries
title: 分页查询 / 滞后查询
---

在 UI 设计中，呈现分页数据是一种非常常见的模式。在 TanStack Query 中，通过在查询键中包含页面信息，它可以“自动工作”：

[//]: # 'Example'
```tsx
const result = useQuery({
  queryKey: ['projects', page],
  queryFn: fetchProjects,
});

```
[//]: # 'Example'

然而，如果您运行这个简单的示例，您可能会注意到一些奇怪的地方：

**UI 在`success`和`loading`状态之间跳动，因为每个新页面被视为全新的查询。**

这种体验并不理想，不幸的是，这是当今许多工具坚持的工作方式。但是 TanStack Query 不同！正如您可能已经猜到的那样，TanStack Query 具有一个很棒的功能，称为 `keepPreviousData`，它允许我们绕过这一点。

## 使用 `keepPreviousData` 改进分页查询

考虑以下示例，我们理想情况下希望为查询递增一个 pageIndex（或游标）。如果我们使用 `useQuery`，**从技术上讲，它仍然可以正常工作**，但是 UI 会在不同的查询为每个页面或游标创建和销毁时在`success`和`loading`状态之间跳动。通过将 `keepPreviousData` 设置为 `true`，我们可以获得一些新的功能：

- **在请求新数据时，上一次成功获取的数据仍然可用，即使查询键已更改**。
- 新数据到达时，上一个`data`会平稳地被切换以显示新数据。
- 可以使用 `isPreviousData` 来了解查询当前提供的数据。

[//]: # 'Example2'
```tsx
function Todos() {
  const [page, setPage] = React.useState(0);

  const fetchProjects = (page = 0) =>
    fetch('/api/projects?page=' + page).then((res) => res.json());

  const { isLoading, isError, error, data, isFetching, isPreviousData } =
    useQuery({
      queryKey: ['projects', page],
      queryFn: () => fetchProjects(page),
      keepPreviousData: true,
    });

  return (
    <div>
      {isLoading ? (
        <div>Loading...</div>
      ) : isError ? (
        <div>Error: {error.message}</div>
      ) : (
        <div>
          {data.projects.map((project) => (
            <p key={project.id}>{project.name}</p>
          ))}
        </div>
      )}
      <span>Current Page: {page + 1}</span>
      <button
        onClick={() => setPage((old) => Math.max(old - 1, 0))}
        disabled={page === 0}
      >
        Previous Page
      </button>{' '}
      <button
        onClick={() => {
          if (!isPreviousData && data.hasMore) {
            setPage((old) => old + 1);
          }
        }}
        // Disable the Next Page button until we know a next page is available
        disabled={isPreviousData || !data?.hasMore}
      >
        Next Page
      </button>
      {isFetching ? <span> Loading...</span> : null}{' '}
    </div>
  );
}

```
[//]: # 'Example2'

## 使用 `keepPreviousData` 滞后无限查询结果

虽然不太常见，但 `keepPreviousData` 选项在 `useInfiniteQuery` 钩子中也可以完美运行，因此您可以在无限查询键随着时间的推移而更改的情况下，无缝地允许用户继续看到缓存数据。
