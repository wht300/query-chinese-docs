---
id: prefer-query-object-syntax
title: 首选使用 useQuery 的对象语法
---

您可以以两种不同的方式使用 [`useQuery`](https://tanstack.com/query/v4/docs/reference/useQuery)。

标准方式：

```tsx
useQuery(queryKey, queryFn?, options?)

// 或者

useQuery(options)
```

此规则首选第二种选项，因为它与其他 React Query hook（如 `useQueries`）更一致。在未来的主要版本中，这也将是唯一可用的选项。

## 规则详解

以下是此规则的错误示例代码：

```js
/* eslint "@tanstack/query/prefer-query-object-syntax": "error" */

import { useQuery } from '@tanstack/react-query';

useQuery(queryKey, queryFn, {
  onSuccess,
});

useQuery(queryKey, {
  queryFn,
  onSuccess,
});
```

以下是此规则的正确示例代码：

```js
import { useQuery } from '@tanstack/react-query';

useQuery({
  queryKey,
  queryFn,
  onSuccess,
});
```

## 何时不使用

如果您不关心 `useQuery` 的一致性，则不需要使用此规则。

## 属性

- [x] ✅ 推荐
- [x] 🔧 可修复

## 鸣谢

此规则最初由[KubaJastrz](https://github.com/KubaJastrz)在 [eslint-plugin-react-query](https://github.com/KubaJastrz/eslint-plugin-react-query) 中开发。
