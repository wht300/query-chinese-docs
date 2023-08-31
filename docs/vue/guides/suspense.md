---
id: suspense
title: Suspense (实验性)
---

> NOTE: Vue Query 的 Suspense 模式是实验性的，与 Vue 的 Suspense 本身一样。这些 API 将会改变，并且不应该在生产环境中使用，除非你将 Vue 和 Vue Query 的版本都锁定在与彼此兼容的补丁级别版本上。

Vue Query 也可以与 Vue 的新 [Suspense](https://vuejs.org/guide/built-ins/suspense.html) API 一起使用。

为了做到这一点，你需要用 Vue 提供的 Suspense 组件包裹你的可挂起组件

```vue
<script setup>
import SuspendableComponent from './SuspendableComponent.vue'
</script>

<template>
  <Suspense>
    <template #default>
      <SuspendableComponent />
    </template>
    <template #fallback>
      <div>Loading...</div>
    </template>
  </Suspense>
</template>
```

然后，将可挂起组件中的 `setup` 函数更改为 `async`。然后，你就可以使用由 `vue-query` 提供的异步 `suspense` 函数了。

```vue
<script>
import { defineComponent } from 'vue'
import { useQuery } from '@tanstack/vue-query'

const todoFetcher = async () =>
  await fetch('https://jsonplaceholder.cypress.io/todos').then((response) =>
    response.json(),
  )
export default defineComponent({
  name: 'SuspendableComponent',
  async setup() {
    const { data, suspense } = useQuery(['todos'], todoFetcher)
    await suspense()

    return { data }
  },
})
</script>
```

## Fetch-on-render vs Render-as-you-fetch

Vue Query在`suspense`模式下开箱即用，作为 **Fetch-on-render** 解决方案表现良好，无需额外配置。这意味着当您的组件尝试挂载时，它们将触发查询获取和挂起，但只有在您导入并挂载它们之后才会这样做。如果您想将其提升到下一个级别并实现 **Render-as-you-fetch** 模式，我们建议在路由回调和/或用户交互事件上实现[Prefetching](../guides/prefetching),以便在组件挂载之前开始加载查询，甚至可能在您开始导入或挂载其父组件之前就开始加载。

### Fetch-on-render
是以渲染为目标的数据获取方式。在这种模式下，当组件尝试挂载时，会触发查询获取并挂起，但只有在导入和挂载组件之后才会发生。这是 Vue Query 在 suspense 模式下的默认行为。

### Render-as-you-fetch
是一种按需获取数据并边渲染的模式。它建议在路由回调和/或用户交互事件上实施Prefetching，以便在组件被挂载之前开始加载查询，并且甚至可能在导入或挂载其父组件之前就进行预加载。
