---
id: default-query-function
title: Default Query Function（默认查询函数）
ref: docs/react/guides/default-query-function.md
---

[//]: # 'Example'

```tsx
// Define a default query function that will receive the query key
// 定义一个默认的查询函数，它将接收查询键
const defaultQueryFn = async ({ queryKey }) => {
  const { data } = await axios.get(
    `https://jsonplaceholder.typicode.com${queryKey[0]}`,
  )
  return data
}

// provide the default query function to your app with defaultOptions
// 使用 defaultOptions 将默认查询函数提供给您的应用程序
const vueQueryPluginOptions: VueQueryPluginOptions = {
  queryClientConfig: {
    defaultOptions: { queries: { queryFn: defaultQueryFn } },
  },
}
app.use(VueQueryPlugin, vueQueryPluginOptions)

// All you have to do now is pass a key!
// 现在您只需要传递一个键即可！
const { status, data, error, isFetching } = useQuery({
  queryKey: [`/posts/${postId}`],
})
```

[//]: # 'Example'
