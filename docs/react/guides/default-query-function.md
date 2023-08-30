---
id: default-query-function
title: 默认查询函数
---

如果出于任何原因，您希望能够共享整个应用程序的相同查询函数，并仅使用查询键来标识它应该获取什么，您可以通过为 TanStack Query 提供一个**默认查询函数**来实现：

[//]: # 'Example'

```tsx
// 定义一个默认的查询函数，该函数将接收查询键
const defaultQueryFn = async ({ queryKey }) => {
  const { data } = await axios.get(
    `https://jsonplaceholder.typicode.com${queryKey[0]}`,
  )
  return data
}

// 使用 defaultOptions 将默认查询函数提供给您的应用程序
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      queryFn: defaultQueryFn,
    },
  },
})

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <YourApp />
    </QueryClientProvider>
  )
}

// 现在您只需要传递一个键！
function Posts() {
  const { status, data, error, isFetching } = useQuery({ queryKey: ['/posts'] })

  // ...
}

// 您甚至可以省略 queryFn，直接进入选项
function Post({ postId }) {
  const { status, data, error, isFetching } = useQuery({
    queryKey: [`/posts/${postId}`],
    enabled: !!postId,
  })

  // ...
}
```

[//]: # 'Example'

如果您想要覆盖默认的 queryFn，您可以像平常一样提供自己的查询函数。
