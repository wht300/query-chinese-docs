---
id: infinite-queries
title: 无限查询
---

在 UI 中，渲染可以将额外的数据“加载更多”到现有数据集中或进行“无限滚动”也是一种非常常见的模式。TanStack Query 支持一个名为 `useInfiniteQuery` 的有用版本，用于查询这些类型的列表。

使用 `useInfiniteQuery` 时，您会注意到一些不同之处：

- `data` 现在是一个包含无限查询数据的对象：
- `data.pages` 数组，包含已获取的页面
- `data.pageParams` 数组，包含用于获取页面的页面参数
- 现在可用 `fetchNextPage` 和 `fetchPreviousPage` 函数
- 对于确定是否有更多数据要加载以及获取信息的情况，现在可用 `getNextPageParam` 和 `getPreviousPageParam` 选项。此信息作为查询函数的附加参数提供（在调用 `fetchNextPage` 或 `fetchPreviousPage` 函数时可以选择覆盖）
- 现在有一个 `hasNextPage` 布尔值，如果 `getNextPageParam` 返回除 `undefined` 以外的值，则为 `true`
- 现在有一个 `hasPreviousPage` 布尔值，如果 `getPreviousPageParam` 返回除 `undefined` 以外的值，则为 `true`
- 现在可用 `isFetchingNextPage` 和 `isFetchingPreviousPage` 布尔值，以区分后台刷新状态和加载更多状态

> 注意：当在查询中使用诸如 `initialData` 或 `select` 之类的选项时，请确保在重新构造数据时仍然包含 `data.pages` 和 `data.pageParams` 属性，否则查询将在其返回中覆盖您的更改！

## 示例

假设我们有一个返回每次基于 `cursor` 索引的 `projects` 页面的 API，每次返回 3 个页面，同时还有一个用于获取下一组项目的游标：

```tsx
fetch('/api/projects?cursor=0')
// { data: [...], nextCursor: 3}
fetch('/api/projects?cursor=3')
// { data: [...], nextCursor: 6}
fetch('/api/projects?cursor=6')
// { data: [...], nextCursor: 9}
fetch('/api/projects?cursor=9')
// { data: [...] }
```

有了这些信息，我们可以通过以下方式创建“加载更多”界面：

- 默认情况下，等待 `useInfiniteQuery` 请求第一组数据
- 在 `getNextPageParam` 中返回下一个查询的信息
- 调用 `fetchNextPage` 函数

> 注意：非常重要的是，除非您希望它们覆盖来自 `getNextPageParam` 函数返回的 `pageParam` 数据，否则不要调用带有参数的 `fetchNextPage`。例如，不要这样做：`<button onClick={fetchNextPage} />`，因为这会将 onClick 事件发送到 `fetchNextPage` 函数。

[//]: # 'Example'

```tsx
import { useInfiniteQuery } from '@tanstack/react-query';

function Projects() {
  const fetchProjects = async ({ pageParam = 0 }) => {
    const res = await fetch('/api/projects?cursor=' + pageParam);
    return res.json();
  };

  const {
    data,
    error,
    fetchNextPage,
    hasNextPage,
    isFetching,
    isFetchingNextPage,
    status,
  } = useInfiniteQuery({
    queryKey: ['projects'],
    queryFn: fetchProjects,
    getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
  });

  return status === 'loading' ? (
    <p>Loading...</p>
  ) : status === 'error' ? (
    <p>Error: {error.message}</p>
  ) : (
    <>
      {data.pages.map((group, i) => (
        <React.Fragment key={i}>
          {group.data.map((project) => (
            <p key={project.id}>{project.name}</p>
          ))}
        </React.Fragment>
      ))}
      <div>
        <button
          onClick={() => fetchNextPage()}
          disabled={!hasNextPage || isFetchingNextPage}
        >
          {isFetchingNextPage
            ? 'Loading more...'
            : hasNextPage
              ? 'Load More'
              : 'Nothing more to load'}
        </button>
      </div>
      <div>{isFetching && !isFetchingNextPage ? 'Fetching...' : null}</div>
    </>
  );
}

```

[//]: # 'Example'

## 无限查询需要重新获取时会发生什么？

当无限查询变得陈旧并需要重新获取时，每个分组都会从第一个分组开始`顺序`获取。这样即使基础数据被改变，我们也不会使用陈旧的游标，可能会出现

重复或跳过记录的情况。如果无限查询的结果从查询缓存中被删除，分页会从初始状态重新开始，只有初始分组会被请求。

### refetchPage

如果您只想主动重新获取所有页面的子集，可以将 `refetchPage` 函数传递给从 `useInfiniteQuery` 返回的 `refetch`。

[//]: # 'Example2'

```tsx
const { refetch } = useInfiniteQuery({
  queryKey: ['projects'],
  queryFn: fetchProjects,
  getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
})

// 仅重新获取第一页
refetch({ refetchPage: (page, index) => index === 0 })
```

[//]: # 'Example2'

您还可以将此函数作为第二个参数（`queryFilters`）传递给 [queryClient.refetchQueries](../reference/QueryClient#queryclientrefetchqueries)、[queryClient.invalidateQueries](../reference/QueryClient#queryclientinvalidatequeries) 或 [queryClient.resetQueries](../reference/QueryClient#queryclientresetqueries)。

**签名**

- `refetchPage: (page: TData, index: number, allPages: TData[]) => boolean`

该函数对每个页面执行，只有在此函数返回 `true` 的页面将被重新获取。

## 如果我需要将自定义信息传递给我的查询函数怎么办？

默认情况下，从 `getNextPageParam` 返回的变量将被提供给查询函数，但在某些情况下，您可能希望覆盖此内容。您可以通过将自定义变量传递给 `fetchNextPage` 函数来覆盖默认变量，如下所示：

[//]: # 'Example3'

```tsx
function Projects() {
  const fetchProjects = ({ pageParam = 0 }) =>
    fetch('/api/projects?cursor=' + pageParam)

  const {
    status,
    data,
    isFetching,
    isFetchingNextPage,
    fetchNextPage,
    hasNextPage,
  } = useInfiniteQuery({
    queryKey: ['projects'],
    queryFn: fetchProjects,
    getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
  })

// 传递您自己的页面参数
  const skipToCursor50 = () => fetchNextPage({ pageParam: 50 })
}

```

[//]: # 'Example3'

## 如果我想要实现双向无限列表怎么办？

通过使用 `getPreviousPageParam`、`fetchPreviousPage`、`hasPreviousPage` 和 `isFetchingPreviousPage` 属性和函数，可以实现双向列表。

[//]: # 'Example4'

```tsx
useInfiniteQuery({
  queryKey: ['projects'],
  queryFn: fetchProjects,
  getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
  getPreviousPageParam: (firstPage, pages) => firstPage.prevCursor,
})
```

[//]: # 'Example4'

## 如果我想以相反的顺序显示页面怎么办？

有时您可能希望以相反的顺序显示页面。如果是这样，您可以使用 `select` 选项：

[//]: # 'Example5'

```tsx
useInfiniteQuery({
  queryKey: ['projects'],
  queryFn: fetchProjects,
  select: (data) => ({
    pages: [...data.pages].reverse(),
    pageParams: [...data.pageParams].reverse(),
  }),
})
```

[//]: # 'Example5'

## 如果我想要手动更新无限查询怎么办？

### 手动删除第一页：

[//]: # 'Example6'

```tsx
queryClient.setQueryData(['projects'], (data) => ({
  pages: data.pages.slice(1),
  pageParams: data.pageParams.slice(1),
}))
```

[//]: # 'Example6'

### 手动从单个页面中删除单个值：

[//]: # 'Example7'

```tsx
const newPagesArray =
  oldPagesArray?.pages.map((page) =>
    page.filter((val) => val.id !== updatedId),
  ) ?? []

queryClient.setQueryData(['projects'], (data) => ({
  pages: newPagesArray,
  pageParams: data.pageParams,
}))
```

[//]: # 'Example7'

### 仅保留第一页：

[//]: # 'Example8'

```tsx
queryClient.setQueryData(['projects'], (data) => ({
  pages: data.pages.slice(0,1),
  pageParams: data.pageParams.slice(0,1),
}))
```

[//]: # 'Example8'

请确保始终保持相同的页面和页面参数的数据结构！
