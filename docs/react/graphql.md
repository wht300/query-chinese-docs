---
id: graphql
title: GraphQL
---

由于 React Query 的获取机制在本质上是基于 Promise，你可以与任何异步数据获取客户端一起使用 React Query，包括 GraphQL！

> 请注意，React Query 不支持规范化缓存。虽然绝大多数用户实际上不需要规范化缓存，或者实际受益并不如他们所认为的那么多，但可能有非常罕见的情况需要使用它，所以务必首先与我们核实，以确保你真正需要它！

[//]: # 'Codegen'

## 类型安全和代码生成

将 React Query 与 `graphql-request^5` 和 [GraphQL Code Generator](https://graphql-code-generator.com/) 结合使用，可以提供完全类型化的 GraphQL 操作：

```tsx
import request from 'graphql-request'
import { useQuery } from '@tanstack/react-query'

import { graphql } from './gql/gql'

const allFilmsWithVariablesQueryDocument = graphql(/* GraphQL */ `
query allFilmsWithVariablesQuery($first: Int!) {
allFilms(first: $first) {
edges {
node {
id
title
}
}
}
}
`)

function App() {
// `data` 具有完全的类型信息！
const { data } = useQuery({
queryKey: ['films'],
queryFn: async () =>
request(
'https://swapi-graphql.netlify.app/.netlify/functions/index',
allFilmsWithVariablesQueryDocument,
// 变量也会进行类型检查！
{ first: 10 },
),
})
// ...
}
```

_你可以在[存储库中找到完整的示例](https://github.com/dotansimha/graphql-code-generator/tree/7c25c4eeb77f88677fd79da557b7b5326e3f3950/examples/front-end/react/tanstack-react-query)_

在 [GraphQL Code Generator 文档的专用指南](https://www.the-guild.dev/graphql/codegen/docs/guides/react-vue)中开始使用。

[//]: # 'Codegen'
