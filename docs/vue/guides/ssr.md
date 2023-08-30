---
id: ssr
title: 服务器端渲染（SSR）
---

Vue Query 支持在服务器上预取多个查询，然后将这些查询 _脱水_ 到 queryClient。这意味着服务器可以预渲染在页面加载时立即可用的标记，并且一旦 JS 可用，Vue Query 就可以升级或 _水合_ 这些查询，以便使用库的全部功能。这包括在客户端重新获取这些查询，如果自从它们在服务器上呈现时就已变为陈旧。

## 使用 Nuxt.js

### Nuxt 3

首先，在您的 `plugins` 目录中创建 `vue-query.ts` 文件，其内容如下：

```ts
import type { DehydratedState, VueQueryPluginOptions } from '@tanstack/vue-query'
import { VueQueryPlugin, QueryClient, hydrate, dehydrate } from '@tanstack/vue-query'
// Nuxt 3 app aliases
import { useState } from '#app'

export default defineNuxtPlugin((nuxt) => {
  const vueQueryState = useState<DehydratedState | null>('vue-query')

  // 在这里修改您的 Vue Query 全局设置
  const queryClient = new QueryClient({
    defaultOptions: { queries: { staleTime: 5000 } },
  })
  const options: VueQueryPluginOptions = { queryClient }

  nuxt.vueApp.use(VueQueryPlugin, options)

  if (process.server) {
    nuxt.hooks.hook('app:rendered', () => {
      vueQueryState.value = dehydrate(queryClient)
    })
  }

  if (process.client) {
    nuxt.hooks.hook('app:created', () => {
      hydrate(queryClient, vueQueryState.value)
    })
  }
})
```

现在，您可以在页面中使用 `onServerPrefetch` 预取一些数据。

- 使用 `queryClient.prefetchQuery` 或 `suspense` 预取您需要的所有查询

```ts
export default defineComponent({
  setup() {
    const { data, suspense } = useQuery('test', fetcher)

    onServerPrefetch(async () => {
      await suspense()
    })

    return { data }
  },
})
```

### Nuxt 2

首先，在您的 `plugins` 目录中创建 `vue-query.js` 文件，其内容如下：

```js
import Vue from 'vue'
import { VueQueryPlugin, QueryClient, hydrate } from '@tanstack/vue-query'

export default (context) => {
  // 在这里修改您的 Vue Query 全局设置
  const queryClient = new QueryClient({
    defaultOptions: { queries: { staleTime: 5000 } },
  })
  const options = { queryClient }

  Vue.use(VueQueryPlugin, options)

  if (process.client) {
    if (context.nuxtState && context.nuxtState['vue-query']) {
      hydrate(queryClient, context.nuxtState['vue-query'])
    }
  }
}
```

将此插件添加到您的 `nuxt.config.js` 文件中

```js
module.exports = {
  ...
  plugins: ['~/plugins/vue-query.js'],
};
```

现在，您可以在页面中使用 `onServerPrefetch` 预取一些数据。

- 使用 `useContext` 获取 nuxt 上下文
- 使用 `useQueryClient` 获取 `queryClient` 的服务器端实例
- 使用 `queryClient.prefetchQuery` 或 `suspense` 预取您需要的所有查询
- 将 `queryClient` 脱水到 `nuxtContext`

```js
// pages/todos.vue
<template>
  <div>
    <button @click="refetch">重新获取</button>
    <p>{{ data }}</p>
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
  onServerPrefetch,
  useContext,
} from "@nuxtjs/composition-api";
import { useQuery, useQueryClient, dehydrate } from "@tanstack/vue-query";

export default defineComponent({
  setup() {
    // 这将在服务器上预取并发送
    const { refetch, data, suspense } = useQuery("todos", getTodos);
    // 这不会被预取，它将在客户端上开始获取
    const { data2 } = useQuery("todos2", getTodos);

    onServerPrefetch(async () => {
     const { ssrContext } = useContext();
      const queryClient = useQueryClient();
      await suspense();

      ssrContext.nuxt.vueQueryState = dehydrate(queryClient);
    });

    return {
      refetch,
      data,
    };
  },
});
</script>
```

正如演示的那样，预取一些查询，并让其他查询在 `queryClient` 上获取，是可以的。这意味着您可以通过添加或删除特定查询的 `prefetchQuery` 或 `suspense` 来控制服务器渲染的内容。

## 使用 Vite SSR

使用 [vite-ssr](https://github.com/frandiox/vite-ssr) 将 VueQuery 客户端状态与其同步，以便在 DOM 中序列化它：

```js
// main.js (入口点)
import App from './App.vue'
import viteSSR from 'vite-ssr/vue'
import { QueryClient, VueQueryPlugin, hydrate, dehydrate } from '@tanstack/vue-query'

export default viteSSR(App, { routes: [] }, ({ app, initialState }) => {
  // -- 这是 Vite SSR 的主要挂钩，每个请求调用一次

  // 创建一个新的 VueQuery 客户端
  const queryClient = new QueryClient()

  // 将初始状态与客户端状态同步
  if (import.meta.env.SSR) {
    // 指示在 SSR 期间如何访问和序列化 VueQuery 状态
    initialState.vueQueryState = { toJSON: () => dehydrate(queryClient) }
  } else {
    // 在浏览器中重用现有状态
    hydrate(queryClient, initialState.vueQueryState)
  }

  // 安装并为应用程序组件提供客户端
  app.use(VueQueryPlugin, { queryClient })
})
```

然后，从任何组件中调用 VueQuery，使用 Vue 的 `onServerPrefetch`：

```html
<!-- MyComponent.vue -->
<template>
  <div>
    <button @click="refetch">重新获取</button>
    <p>{{ data }}</p>
  </div>
</template>

<script setup>
  import { useQuery } from '@tanstack/vue-query'


 import { onServerPrefetch } from 'vue'

  // 这将在服务器上预取并发送
  const { refetch, data, suspense } = useQuery('todos', getTodos)
  onServerPrefetch(suspense)
</script>
```

## 提示、技巧和注意事项

### 仅成功的查询包括在脱水中

任何带有错误的查询都会自动从脱水中排除。这意味着默认行为是假装这些查询在服务器上从未加载过，通常显示加载状态，并在 queryClient 上重试这些查询。这发生在任何错误情况下。

有时这种行为是不可取的，也许您希望根据某些错误或查询，在某些错误情况下，呈现一个带有正确状态码的错误页面。在这些情况下，使用 `fetchQuery` 并捕获任何错误以手动处理。

### 新鲜度是从查询在服务器上获取的时间开始计算的

查询的新鲜度取决于其 `dataUpdatedAt` 何时。一个注意点是服务器需要有正确的时间，但使用的是 UTC 时间，因此时区不会影响这一点。

由于 `staleTime` 默认为 `0`，默认情况下，查询将在页面加载时立即在后台重新获取。如果不缓存标记，您可能希望使用较高的 `staleTime` 来避免这种双重获取，特别是在不缓存标记的情况下。

这种刷新陈旧查询的方法非常适用于在 CDN 中缓存标记！您可以将页面自身的缓存时间设置得足够长，以避免不必要地在服务器上重新渲染页面，但是将查询的 `staleTime` 配置为较低，以确保数据在用户访问页面时在后台自动刷新，如果数据自从最初呈现在服务器上后已过时。

### 服务器上的高内存消耗

如果您为每个请求创建 `QueryClient`，Vue Query 会为此客户端创建隔离的缓存，在 `cacheTime` 期间将其保存在内存中。在该期间高请求量的情况下，这可能导致服务器上的内存消耗较高。

在服务器上，默认情况下 `cacheTime` 为 `Infinity`，这会禁用手动垃圾回收，并在请求结束后自动清除内存。如果您明确设置非无限制的 `cacheTime`，那么您将负责及早清除缓存。

为了在不再需要缓存时清除缓存以及降低内存消耗，您可以在处理请求并将脱水状态发送到客户端后，添加对 [`queryClient.clear()`](../reference/QueryClient#queryclientclear) 的调用。

或者，您可以设置较小的 `cacheTime`。
```
