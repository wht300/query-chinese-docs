---
id: custom-client
title: Custom Client（自定义 Client）
---

### Custom client（自定义 Client）

Vue Query allows providing custom `QueryClient` for Vue context.
Vue Query 允许为 Vue 上下文提供自定义的 `QueryClient`。

It might be handy when you need to create `QueryClient` beforehand to integrate it with other libraries that do not have access to the Vue context.
当您需要预先创建 `QueryClient` 并将其集成到其他没有访问 Vue 上下文的库中时，这可能非常方便。

For this reason, `VueQueryPlugin` accepts either `QueryClientConfig` or `QueryClient` as a plugin options.
出于这个原因，`VueQueryPlugin` 接受 `QueryClientConfig` 或 `QueryClient` 作为插件选项。

If You provide `QueryClientConfig`, `QueryClient` instance will be created internally and provided to Vue context.
如果您提供了 `QueryClientConfig`，则会在内部创建 `QueryClient` 实例并提供给 Vue 上下文。

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

### Custom context key(自定义上下文 key)

You can also customize the key under which `QueryClient` will be accessible in Vue context. This can be usefull is you want to avoid name clashing between multiple apps on the same page with Vue2.
您还可以自定义 `QueryClient` 在 Vue 上下文中可访问的键。如果您想要避免在同一页上使用 Vue2 的多个应用程序之间出现名称冲突，这可能很有用。

It works both with default, and custom `QueryClient`
它适用于默认和自定义 `QueryClient`。


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

To use the custom client key, You have to provide it as a query options
要使用自定义客户端键，您必须将其作为查询选项提供。

```js
useQuery({
  queryKey: ['query1'],
  queryFn: fetcher,
  queryClientKey: 'foo',
})
```

Internally custom key will be combined with default query key as a suffix. But user do not have to worry about it.
在内部，自定义键将与默认查询键组合为后缀。但用户不必担心它。

```tsx
const vueQueryPluginOptions: VueQueryPluginOptions = {
  queryClientKey: 'Foo',
}
app.use(VueQueryPlugin, vueQueryPluginOptions) // -> VUE_QUERY_CLIENT:Foo
```
