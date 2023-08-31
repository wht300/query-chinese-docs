---
id: OnlineManager
title: 在线状态管理器 (OnlineManager)
---

`OnlineManager` 在 TanStack Query 中管理在线状态。

它可以用于更改默认的事件监听器或手动更改在线状态。

它的可用方法包括：

- [`setEventListener`](#onlinemanagerseteventlistener)
- [`setOnline`](#onlinemanagersetonline)
- [`isOnline`](#onlinemanagerisonline)

## `onlineManager.setEventListener`

`setEventListener` 可用于设置自定义事件监听器：

```tsx
import NetInfo from '@react-native-community/netinfo'
import { onlineManager } from '@tanstack/react-query'

onlineManager.setEventListener(setOnline => {
  return NetInfo.addEventListener(state => {
    setOnline(!!state.isConnected)
  })
})
```

## `onlineManager.setOnline`

`setOnline` 可以用于手动设置在线状态。将值设为 `undefined` 以回退到默认的在线检查。

```tsx
import { onlineManager } from '@tanstack/react-query'

// 设置为在线状态
onlineManager.setOnline(true)

// 设置为离线状态
onlineManager.setOnline(false)

// 回退到默认的在线检查
onlineManager.setOnline(undefined)
```

**选项**

- `online: boolean | undefined`

## `onlineManager.isOnline`

`isOnline` 可以用于获取当前的在线状态。

```tsx
const isOnline = onlineManager.isOnline()
```
