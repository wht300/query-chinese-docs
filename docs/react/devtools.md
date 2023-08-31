---
id: devtools
title: 开发者工具 (Devtools)
---

挥动你的手臂，欢呼雀跃，因为 React Query 自带了专用的开发者工具！ 🥳

当你开始使用 React Query 的旅程时，你肯定会想要使用这些开发者工具。它们有助于可视化 React Query 的所有内部工作原理，如果你在紧要关头遇到问题，它们很可能会为你节省大量的调试时间！

> 请注意，目前开发者工具**不支持 React Native**。如果你想帮助我们使开发者工具具备平台无关性，请告诉我们！

> 另请注意，你可以使用这些开发者工具来观察查询，但**无法观察变更操作(mutation)**。

## 安装和引入开发者工具

开发者工具是一个独立的包，你需要安装它：

```bash
$ npm i @tanstack/react-query-devtools
# 或者
$ pnpm add @tanstack/react-query-devtools
# 或者
$ yarn add @tanstack/react-query-devtools
```

你可以像这样引入开发者工具：

```tsx
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'
```

默认情况下，React Query Devtools 仅在 `process.env.NODE_ENV === 'development'` 时包含在捆绑包中，因此在生产构建时无需担心排除它们。

## 悬浮模式

悬浮模式将开发者工具作为一个固定的浮动元素挂载在你的应用中，并在屏幕角落提供一个切换按钮来显示和隐藏开发者工具。此切换状态将在重新加载时存储并在本地存储中保留。

将以下代码放置在你的 React 应用程序中的尽可能高的位置。它离页面的根部越近，它的效果就会越好！

```tsx
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'

function App() {
return (
<QueryClientProvider client={queryClient}>
{/* 其他应用程序内容 */}
<ReactQueryDevtools initialIsOpen={false} />
</QueryClientProvider>
)
}
```

### 选项

- `initialIsOpen: Boolean`
- 如果希望开发工具默认为打开状态，请将其设置为 `true`。
- `panelProps: PropsObject`
- 使用此选项向面板添加 props。例如，你可以添加 `className`、`style`（合并和覆盖默认样式）等。
- `closeButtonProps: PropsObject`
- 使用此选项向关闭按钮添加 props。例如，你可以添加 `className`、`style`（合并和覆盖默认样式）、`onClick`（扩展默认处理程序）等。
- `toggleButtonProps: PropsObject`
- 使用此选项向切换按钮添加 props。例如，你可以添加 `className`、`style`（合并和覆盖默认样式）、`onClick`（扩展默认处理程序）等。
- `position?: "top-left" | "top-right" | "bottom-left" | "bottom-right"`
- 默认为 `bottom-left`
- React Query 标志的位置，用于打开和关闭开发者工具面板。
- `panelPosition?: "top" | "bottom" | "left" | "right"`
- 默认为 `bottom`
- React Query 开发者工具面板的位置。
- `context?: React.Context<QueryClient | undefined>`
- 使用此选项来使用自定义的 React Query 上下文。否则，将使用 `defaultContext`。

## 嵌入模式

嵌入模式将开发者工具作为应用程序中的常规组件嵌入其中。然后，你可以按照你喜欢的方式对其进行样式设置！

```tsx
import { ReactQueryDevtoolsPanel } from '@tanstack/react-query-devtools'

function App() {
return (
<QueryClientProvider client={queryClient}>
{/* 其他应用程序内容 */}
<ReactQueryDevtoolsPanel style={styles} className={className} />
</QueryClientProvider>
)
}
```

### 选项

使用这些选项来样式化开发工具。

- `style: StyleObject`
- 用于使用内联样式样式化组件的标准 React 样式对象
- `className: string`
- 用于使用类样式样式化组件的标准 React className 属性
- `showCloseButton?: boolean`
- 在开发者工具面板内显示关闭按钮
- `closeButtonProps: PropsObject`
- 使用此选项向关闭按钮添加 props。例如，你可以添加 `className`、`style`（合并和覆盖默认样式）、`onClick`（扩展默认处理程序）等。

## 生产环境中的开发者工具

开发者工具在生产构建中被排除在外。然而，在生产环境中延迟加载开发者工具可能是可取的：

```tsx
import * as React from 'react'
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'
import { Example } from './Example'

const queryClient = new QueryClient()

const ReactQueryDevtoolsProduction = React.lazy(() =>
import('@tanstack/react-query-devtools/build/lib/index.prod.js').then(
(d) => ({
default: d.ReactQueryDevtools,
}),
),
)

function App() {
const [showDevtools, setShowDevtools] = React.useState(false)

React.useEffect(() => {
// @ts-ignore
window.toggleDevtools = () => setShowDevtools((old) => !old)
}, [])

return (
<QueryClientProvider client={queryClient}>
<Example />
<ReactQueryDevtools initialIsOpen />
{showDevtools && (
<React.Suspense fallback={null}>
<ReactQueryDevtoolsProduction />
</React.Suspense>
)}
</QueryClientProvider>
)
}

export default App
```

这样，调用 `window.toggleDevtools

()` 将下载开发者工具包并显示它们。

### 现代捆绑工具

如果你的捆绑工具支持包导出（package exports），你可以使用以下导入路径：

```tsx
const ReactQueryDevtoolsProduction = React.lazy(() =>
import('@tanstack/react-query-devtools/production').then((d) => ({
default: d.ReactQueryDevtools,
})),
)
```

对于 TypeScript，你需要在 tsconfig 中设置 `moduleResolution: 'nodenext'`，这至少需要 TypeScript v4.7。
