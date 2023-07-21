---
id: installation
title: 安装 - Installation
---

You can install Vue Query via [NPM](https://npmjs.com).
你可以通过 [NPM](https://npmjs.com) 来安装 Vue Query。

### NPM

```bash
$ npm i @tanstack/vue-query
# or
$ pnpm add @tanstack/vue-query
# or
$ yarn add @tanstack/vue-query
```

> Wanna give it a spin before you download? Try out the [basic](/query/v4/docs/vue/examples/vue/basic) example!
> 在下载之前想试试看吗？可以尝试一下[basic](/query/v4/docs/vue/examples/vue/basic)示例！

Vue Query is compatible with Vue 2.x and 3.x

Vue Query 与 Vue 2.x 和 3.x 兼容。

> If you are using Vue 2.6, make sure to also setup [@vue/composition-api](https://github.com/vuejs/composition-api)
> 如果你正在使用 Vue 2.6,请确保也设置了 [@vue/composition-api](https://github.com/vuejs/composition-api)

### Vue Query 初始化 - Vue Query Initialization

Before using Vue Query, you need to initialize it using `VueQueryPlugin`
在使用 Vue Query 之前，你需要使用 `VueQueryPlugin` 进行初始化。

```tsx
import { VueQueryPlugin } from "@tanstack/vue-query";

app.use(VueQueryPlugin)
```

### Use of Composition API with `<script setup>`

All examples in our documentation use [`<script setup>`](https://staging.vuejs.org/api/sfc-script-setup.html) syntax.

我们文档中的所有示例都使用了 [`<script setup>`](https://staging.vuejs.org/api/sfc-script-setup.html)语法。

Vue 2 users can also use that syntax using [this plugin](https://github.com/antfu/unplugin-vue2-script-setup). Please check the plugin documentation for installation details.

Vue 2 用户也可以使用这种语法，通过 [this plugin](https://github.com/antfu/unplugin-vue2-script-setup)。请查看插件文档以获取安装详细信息。

If you are not a fan of `<script setup>` syntax, you can easily translate all the examples into normal Composition API syntax by moving the code under `setup()` function and returning the values used in the template.
如果你不喜欢 `<script setup>` 语法，你可以轻松地将所有示例翻译成普通的 Composition API 语法，方法是将代码移到 `setup()` 函数下并返回模板中使用的值。

```vue
<script setup>
import { useQuery } from "@tanstack/vue-query";

const { isLoading, isFetching, isError, data, error } = useQuery({
  queryKey: ['todos'],
  queryFn: getTodos,
})
</script>

<template>...</template>
```
