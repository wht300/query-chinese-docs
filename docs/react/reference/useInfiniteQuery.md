---
id: useInfiniteQuery
title: useInfiniteQuery
---

```tsx
const {
  fetchNextPage,
  fetchPreviousPage,
  hasNextPage,
  hasPreviousPage,
  isFetchingNextPage,
  isFetchingPreviousPage,
  ...result
} = useInfiniteQuery({
  queryKey,
  queryFn: ({ pageParam = 1 }) => fetchPage(pageParam),
  ...options,
  getNextPageParam: (lastPage, allPages) => lastPage.nextCursor,
  getPreviousPageParam: (firstPage, allPages) => firstPage.prevCursor,
})
```

**选项**

`useInfiniteQuery` 的选项与 [`useQuery` 钩子](../reference/useQuery) 完全相同，并额外添加了以下内容：

- `queryFn: (context: QueryFunctionContext) => Promise<TData>`
  - **必填，但仅在未定义默认查询函数 [`defaultQueryFn`](../guides/default-query-function) 时需要**
  - 查询将用来请求数据的函数。
  - 接收一个 [QueryFunctionContext](../guides/query-functions#queryfunctioncontext)
  - 必须返回一个 promise，该 promise 将解析数据或抛出错误。
  - 确保在需要在下面的 props 中使用时返回数据和 `pageParam`。
- `getNextPageParam: (lastPage, allPages) => unknown | undefined`
  - 当收到此查询的新数据时，此函数会同时接收无限数据列表的最后一页和所有页面的完整数组。
  - 它应该返回一个**单一变量**，该变量将作为传递给查询函数的最后一个可选参数。
  - 返回 `undefined` 表示没有可用的下一页。
- `getPreviousPageParam: (firstPage, allPages) => unknown | undefined`
  - 当收到此查询的新数据时，此函数会同时接收无限数据列表的第一页和所有页面的完整数组。
  - 它应该返回一个**单一变量**，该变量将作为传递给查询函数的最后一个可选参数。
  - 返回 `undefined` 表示没有可用的上一页。

**返回值**

`useInfiniteQuery` 的返回属性与 [`useQuery` 钩子](../reference/useQuery) 的返回值完全相同，此外还添加了以下内容，并且在 `isRefetching` 上有轻微差别：

- `data.pages: TData[]`
  - 包含所有页面的数组。
- `data.pageParams: unknown[]`
  - 包含所有页面参数的数组。
- `isFetchingNextPage: boolean`
  - 在使用 `fetchNextPage` 获取下一页时为 `true`。
- `isFetchingPreviousPage: boolean`
  - 在使用 `fetchPreviousPage` 获取上一页时为 `true`。
- `fetchNextPage: (options?: FetchNextPageOptions) => Promise<UseInfiniteQueryResult>`
  - 此函数允许你获取下一页的结果。
  - `options.pageParam: unknown` 允许你手动指定页面参数，而不是使用 `getNextPageParam`。
  - `options.cancelRefetch: boolean` 如果设置为 `true`，重复调用 `fetchNextPage` 将每次都调用 `fetchPage`，无论前一次调用是否已解析。同时，前一次调用的结果将被忽略。如果设置为 `false`，重复调用 `fetchNextPage` 将不会有任何效果，直到第一次调用已解决。默认为 `true`。
- `fetchPreviousPage: (options?: FetchPreviousPageOptions) => Promise<UseInfiniteQueryResult>`
  - 此函数允许你获取上一页的结果。
  - `options.pageParam: unknown` 允许你手动指定页面参数，而不是使用 `getPreviousPageParam`。
  - `options.cancelRefetch: boolean` 与 `fetchNextPage` 相同。
- `hasNextPage: boolean`
  - 如果有下一页需要获取（通过 `getNextPageParam` 选项判断），将为 `true`。
- `hasPreviousPage: boolean`
  - 如果有上一页需要获取（通过 `getPreviousPageParam` 选项判断），将为 `true`。
- `isRefetching: boolean`
  - 在进行后台重新获取时为 `true`，这不包括初始的 `loading`，以及获取下一页或上一页的情况。
  - 与 `isFetching && !isLoading && !isFetchingNextPage && !isFetchingPreviousPage` 相同。
