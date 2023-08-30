---
id: broadcastQueryClient
title: broadcastQueryClient（实验性功能）
---

> 非常重要：此实用程序目前处于实验阶段。这意味着在次要和补丁版本中会发生重大更改。请自行决定是否使用。如果您选择在实验阶段在生产中依赖此功能，请将版本锁定为补丁级别版本，以避免意外的中断。

`broadcastQueryClient` 是一个用于在具有相同源的浏览器选项卡/窗口之间广播和同步查询客户端状态的实用程序。

## 安装

此实用程序作为一个独立的包提供，在 `'@tanstack/query-broadcast-client-experimental'` 导入下可用。

## 使用方法

导入 `broadcastQueryClient` 函数，将其传递给您的 `QueryClient` 实例，还可以设置一个 `broadcastChannel`。

```tsx
import { broadcastQueryClient } from '@tanstack/query-broadcast-client-experimental'

const queryClient = new QueryClient()

broadcastQueryClient({
  queryClient,
  broadcastChannel: 'my-app',
})
```

## API

### `broadcastQueryClient`

将一个 `QueryClient` 实例和可选的 `broadcastChannel` 传递给此函数。

```tsx
broadcastQueryClient({ queryClient, broadcastChannel })
```

### `选项`

一个选项对象：

```tsx
interface broadcastQueryClient {
  /** 要同步的 QueryClient */
  queryClient: QueryClient
  /** 这是用于在选项卡和窗口之间进行通信的唯一通道名称 */
  broadcastChannel?: string
}
```

默认选项为：

```tsx
{
  broadcastChannel = 'react-query',
}
```
