---
id: window-focus-refetching
title: 窗口焦点重新获取数据
---

如果用户离开你的应用并返回，查询数据已经过时，**TanStack Query 会在后台自动为您请求新的数据**。您可以全局或每个查询使用 `refetchOnWindowFocus` 选项来禁用此功能：

#### 全局禁用

[//]: # 'Example'

```tsx
//
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      refetchOnWindowFocus: false, // 默认值为 true
    },
  },
})

function App() {
  return <QueryClientProvider client={queryClient}>...</QueryClientProvider>
}
```

[//]: # 'Example'

#### 每个查询禁用

[//]: # 'Example2'

```tsx
useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodos,
  refetchOnWindowFocus: false,
})
```

[//]: # 'Example2'

## 自定义窗口焦点事件

在极少数情况下，您可能希望管理自己的窗口焦点事件，以触发 TanStack Query 进行重新验证。为此，TanStack Query 提供了一个 `focusManager.setEventListener` 函数，该函数为您提供在窗口获得焦点时应触发的回调，并允许您设置自己的事件。在调用 `focusManager.setEventListener` 时，之前设置的处理程序会被删除（在大多数情况下，将是默认处理程序），并且您的新处理程序会代替之前的处理程序。例如，这是默认的处理程序：

[//]: # 'Example3'

```tsx
focusManager.setEventListener((handleFocus) => {
  // 监听 visibilitychange 和 focus 事件
  if (typeof window !== 'undefined' && window.addEventListener) {
    window.addEventListener('visibilitychange', handleFocus, false)
    window.addEventListener('focus', handleFocus, false)
  }

  return () => {
    // 一定要在设置新处理程序时取消订阅
    window.removeEventListener('visibilitychange', handleFocus)
    window.removeEventListener('focus', handleFocus)
  }
})
```

[//]: # 'Example3'

## 忽略 iframe 焦点事件

替换焦点处理程序的一个很好的用例是 iframe 事件。在双重触发事件和在应用程序内部使用 iframe 时，iframe 存在检测窗口焦点的问题。如果您遇到此问题，应尽可能使用一个忽略这些事件的事件处理程序。我推荐使用 [这个](https://gist.github.com/tannerlinsley/1d3a2122332107fcd8c9cc379be10d88)！它可以按照以下方式设置：

[//]: # 'Example4'

```tsx
import { focusManager } from '@tanstack/react-query'
import onWindowFocus from './onWindowFocus' // 上面的 gist

focusManager.setEventListener(onWindowFocus) // 完成！
```

[//]: # 'Example4'
[//]: # 'ReactNative'

## 在 React Native 中管理焦点

与其在 `window` 上添加事件监听器，React Native 通过 [`AppState` 模块](https://reactnative.dev/docs/appstate#app-states) 提供了焦点信息。您可以使用 `AppState` 的 "change" 事件在应用状态变为 "active" 时触发更新：

```tsx
import { AppState } from 'react-native'
import { focusManager } from '@tanstack/react-query'

function onAppStateChange(status: AppStateStatus) {
  if (Platform.OS !== 'web') {
    focusManager.setFocused(status === 'active')
  }
}

useEffect(() => {
  const subscription = AppState.addEventListener('change', onAppStateChange)

  return () => subscription.remove()
}, [])
```

[//]: # 'ReactNative'

## 管理焦点状态

[//]: # 'Example5'

```tsx
import { focusManager } from '@tanstack/react-query'

// 覆盖默认的焦点状态
focusManager.setFocused(true)

// 回退到默认的焦点检查
focusManager.setFocused(undefined)
```

[//]: # 'Example5'

## 陷阱与注意事项

某些浏览器内部的对话窗口，比如 `alert()` 弹出的窗口或文件上传对话框（由 `<input type="file" />` 创建），在关闭后可能会触发焦点重新获取数据。这可能会导致不希望的副作用，因为重新获取数据可能会在文件上传处理程序

执行之前触发组件卸载或重新挂载。有关背景和可能的解决方法，请参阅 [GitHub 上的这个问题](https://github.com/tannerlinsley/react-query/issues/2960)。
