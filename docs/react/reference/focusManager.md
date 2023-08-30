---
id: FocusManager
title: 焦点管理器 (FocusManager)
---

`FocusManager` 在 TanStack Query 中管理焦点状态。

它可以用于更改默认的事件监听器或手动更改焦点状态。

它提供的方法有：

- [`setEventListener`](#focusmanagerseteventlistener)
- [`setFocused`](#focusmanagersetfocused)
- [`isFocused`](#focusmanagerisfocused)

## `focusManager.setEventListener`

`setEventListener` 可以用于设置自定义的事件监听器：

```tsx
import { focusManager } from '@tanstack/react-query'

focusManager.setEventListener(handleFocus => {
  // 监听 visibilitychange 和 focus 事件
  if (typeof window !== 'undefined' && window.addEventListener) {
    window.addEventListener('visibilitychange', handleFocus, false)
    window.addEventListener('focus', handleFocus, false)
  }

  return () => {
    // 如果设置了新的处理程序，请务必取消订阅
    window.removeEventListener('visibilitychange', handleFocus)
    window.removeEventListener('focus', handleFocus)
  }
})
```

## `focusManager.setFocused`

`setFocused` 可以用于手动设置焦点状态。将 `undefined` 设置为回退到默认的焦点检查。

```tsx
import { focusManager } from '@tanstack/react-query'

// 设置为有焦点
focusManager.setFocused(true)

// 设置为无焦点
focusManager.setFocused(false)

// 回退到默认的焦点检查
focusManager.setFocused(undefined)
```

**选项**

- `focused: boolean | undefined`

## `focusManager.isFocused`

`isFocused` 可以用于获取当前的焦点状态。

```tsx
const isFocused = focusManager.isFocused()
```
