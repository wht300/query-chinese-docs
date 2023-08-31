---
id: typescript
title: TypeScript
---

React Query 现在是使用 **TypeScript** 编写的，以确保库和您的项目是类型安全的！

需要注意的事项：

- 类型当前需要使用 TypeScript v4.1 或更高版本
- 此存储库中的类型更改被视为**非破坏性**，通常作为**补丁**版本发布（否则每次类型增强都会是一个主要版本！）。
- 强烈建议您将 react-query 包的版本锁定到特定的补丁版本，并根据类型可能在任何发布之间进行修复或升级的期望进行升级
- React Query 的非类型相关的公共 API 仍然严格遵循 semver。

## Type Inference(类型推断)

在 React Query 中，类型通常非常流畅，因此您不必自己提供类型注释

[//]: # 'TypeInference1'

```tsx
const { data } = useQuery({
//    ^? const data: number | undefined
queryKey: ['test'],
queryFn: () => Promise.resolve(5),
})
```

[typescript playground](https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAbzgVwM4FMCKz1QJ5wC+cAZlBCHAORToCGAxjALQCOO+VAsAFC8MQAdqnhIAJnRh0icALwoM2XHgAUAbSqDkIAEa4qAXQA0cFQEo5APjgAFciGAYAdLVQQANgDd0KgKxmzXgB6ILgw8IA9AH5eIA)

[//]: # 'TypeInference1'
[//]: # 'TypeInference2'

```tsx
const { data } = useQuery({
//      ^? const data: string | undefined
queryKey: ['test'],
queryFn: () => Promise.resolve(5),
select: (data) => data.toString(),
})
```

[typescript playground](https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAbzgVwM4FMCKz1QJ5wC+cAZlBCHAORToCGAxjALQCOO+VAsAFC8MQAdqnhIAJnRh0icALwoM2XHgAUAbSox0IqgF0ANHBUBKOQD44ABXIhgGAHS1UEADYA3dCoCsxw0gwu6EwAXHASUuZhknT2MBAAyjBQwIIA5iaExrwA9Nlw+QUAegD8vEA)

[//]: # 'TypeInference2'

这在您的 `queryFn` 具有明确定义的返回类型的情况下效果最好。请记住，大多数数据获取库默认返回 `any`，因此请确保将其提取到正确类型的函数中：

[//]: # 'TypeInference3'

```tsx
const fetchGroups = (): Promise<Group[]> =>
axios.get('/groups').then((response) => response.data)

const { data } = useQuery({ queryKey: ['groups'], queryFn: fetchGroups })
//      ^? const data: Group[] | undefined
```

[typescript playground](https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAbzgVwM4FMCKz1QJ5wC+cAZlBCHAORToCGAxjALQCOO+VAsAFCiSw4dAB7AIqUuUpURY1Nx68YeMOjgBxcsjBwAvIjjAAJgC44AO2QgARriK9eDCOdTwS6GAwAWmiNon6ABQAlGYAClLAGAA8vtoA2gC6AHx6qbLiAHQA5h6BVAD02Vpg8sGZMF7o5oG0qJAuarqpdQ0YmUZ0MHTBDjxOLvBInd1EeigY2Lh4gfFUxX6lVIkANKQe3nGlvTwFBXAHhwB6APxwA65wI3RmW0lwAD4o5kboJMDm6Ea8QA)

[//]: # 'TypeInference3'

## 类型缩小

React Query 使用一个[有辨别联合类型](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-func.html#discriminated-unions)来表示查询结果，由 `status` 字段和派生的状态布尔标志进行辨别。这将允许您检查例如 `success` 状态以使 `data` 定义：

[//]: # 'TypeNarrowing'

```tsx
const { data, isSuccess } = useQuery({
queryKey: ['test'],
queryFn: () => Promise.resolve(5),
})

if (isSuccess) {
data
//  ^? const data: number
}
```

[typescript playground](https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAbzgVwM4FMCKz1QJ5wC+cAZlBCHAORToCGAxjALQCOO+VAsAFC8MQAdqnhIAJnRh0ANHGCoAysgYN0qVETgBeFBmy48ACgDaVGGphUAurMMBKbQD44ABXIh56AHS1UEADYAbuiGAKx2dry8wCRwhvJKKmqoDgi8cBlwElK8APS5GQB6APy8hLxAA)

[//]: # 'TypeNarrowing'

## 对错误字段进行类型定义

错误的类型默认为 `unknown`。这与 TypeScript 在 catch 子句中默认给您的内容一致（请参阅 [useUnknownInCatchVariables](https://devblogs.microsoft.com/typescript/announcing-typescript-4-4/#use-unknown-catch-variables)）。与 `error` 一起工作的最安全的方法是执行运行时检查；另一种方法是显式地为 `data` 和 `error` 定义类型：

[//]: # 'TypingError'

```tsx
const { error } = useQuery({ queryKey: ['groups'], queryFn: fetchGroups })
//      ^? const error:

unknown

if (error instanceof Error) {
error
// ^? const error: Error
}
```

[typescript playground](https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAbzgVwM4FMCKz1QJ5wC+cAZlBCHAORToCGAxjALQCOO+VAsAFCiSw4dAB7AIqUuUpURY1Nx68YeMOjgBxcsjBwAvIjjAAJgC44AO2QgARriK9eDCOdTwS6GAwAWmiNon6ABQAlGYAClLAGAA8vtoA2gC6AHx6qbLiAHQA5h6BVAD02Vpg8sGZMF7o5oG0qJAuarqpdQ0YmUZ0MHTBDjxOLvBIuORQRHooGNi4eIHxVMV+pVSJADSkHt5xpb08BQVwh0cAegD8fcAkcIEj0IaDdOYM6BBXAKJQo8GIvIe3ULx9nAzrxCEA)

[//]: # 'TypingError'
[//]: # 'TypingError2'

```tsx
const { error } = useQuery<Group[], Error>(['groups'], fetchGroups)
//      ^? const error: Error | null
```

[typescript playground](https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAbzgVwM4FMCKz1QJ5wC+cAZlBCHAORToCGAxjALQCOO+VAsAFCiSw4dAB7AIqUuUpURY1Nx68YeMOjgBxcsjBwAvIjjAAJgC44AO2QgARriK9eDCOdTwS6GAwAWmiNon6ABQAlGYAClLAGAA8vtoA2gC6AHx6qbLiAHQA5h6BVAD02Vpg8sGZMF7o5oG0qJAuarqpdQ0YmUZ0MHTBDjxOLvBIuORQRHooGNi4eLElSQA0cACiUKPJgfFUxX6lVIlL7p4+Jai9PAUFcNc3AHoA-LxAA)

[//]: # 'TypingError2'

## 进一步阅读

有关类型推断的技巧和窍门，请查看社区资源中的 [React Query and TypeScript](../community/tkdodos-blog#6-react-query-and-typescript)。要了解如何获得最佳的类型安全性，您可以阅读 [Type-safe React Query](../community/tkdodos-blog#19-type-safe-react-query)。

[//]: # 'Materials'

[//]: # 'Materials'
