---
id: custom-client
title: 自定义 Client
---

### 自定义客户端（Custom Client）

Vue Query 允许在 Vue 上下文中提供自定义的 `QueryClient`。

当您需要预先创建 `QueryClient` 以便将其与其他无法访问 Vue 上下文的库集成时，这可能会很方便。

出于这个原因，`VueQueryPlugin` 可以接受 `QueryClientConfig` 或 `QueryClient` 作为插件选项。

如果您提供了 `QueryClientConfig`，则 `QueryClient` 实例将在内部创建，并提供给 Vue 上下文。

```tsx
const vueQueryPluginOptions: VueQueryPluginOptions = {
  queryClientConfig: {
    defaultOptions: { queries: { staleTime: 3600 } },
  },
}
app.use(VueQueryPlugin, vueQueryPluginOptions)
```

```tsx
const myClient = new QueryClient(queryClientConfig)
const vueQueryPluginOptions: VueQueryPluginOptions = {
  queryClient: myClient,
}
app.use(VueQueryPlugin, vueQueryPluginOptions)
```

### 自定义上下文键（Custom Context Key）

您还可以自定义在其中访问 `QueryClient` 的键。如果您希望在同一页上具有多个 Vue2 应用程序之间避免名称冲突，这可能会很有用。

它适用于默认和自定义的 `QueryClient`。

```tsx
const vueQueryPluginOptions: VueQueryPluginOptions = {
  queryClientKey: 'Foo',
}
app.use(VueQueryPlugin, vueQueryPluginOptions)
```

```tsx
const myClient = new QueryClient(queryClientConfig)
const vueQueryPluginOptions: VueQueryPluginOptions = {
  queryClient: myClient,
  queryClientKey: 'Foo',
}
app.use(VueQueryPlugin, vueQueryPluginOptions)
```

要使用自定义客户端键，您必须将其提供为查询选项

```js
useQuery({
  queryKey: ['query1'],
  queryFn: fetcher,
  queryClientKey: 'foo',
})
```

内部上，自定义键将与默认查询键组合为后缀。但用户不必担心这一点。

```tsx
const vueQueryPluginOptions: VueQueryPluginOptions = {
  queryClientKey: 'Foo',
}
app.use(VueQueryPlugin, vueQueryPluginOptions) // -> VUE_QUERY_CLIENT:Foo
```
