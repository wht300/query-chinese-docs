---
id: liaoliao666-react-query-kit
title: React Query Kit
---

🕊️ 一个用于 ReactQuery 的工具包，使 ReactQuery 钩子可重用且类型安全。

## 你能从中受益

- 将 `queryKey` 与 `queryFn` 强关联
- 以类型安全的方式管理 `queryKey`
- 快速生成自定义的 ReactQuery hook
- 使 `queryClient` 的操作与自定义的 ReactQuery 钩子明确关联
- 更轻松、更清晰地为自定义的 ReactQuery 钩子设置默认选项

## 安装

此模块通过 [NPM](https://www.npmjs.com/package/react-query-kit) 分发，应作为项目的 `dependencies` 之一进行安装：

```bash
$ npm i react-query-kit
# 或者
$ pnpm add react-query-kit
# 或者
$ yarn add react-query-kit
```

## 在 nextjs 中快速开始

[CodeSandbox](https://codesandbox.io/s/example-react-query-kit-nextjs-uldl88)

```tsx
import { QueryClient, dehydrate } from '@tanstack/react-query'
import { createQuery } from 'react-query-kit'

type Response = { title: string; content: string }
type Variables = { id: number }

const usePost = createQuery<Response, Variables, Error>({
  primaryKey: '/posts',
  queryFn: ({ queryKey: [primaryKey, variables] }) => {
    // primaryKey 等于 '/posts'
    return fetch(`${primaryKey}/${variables.id}`).then(res => res.json())
  },
  suspense: true
})

const variables = { id: 1 }

export default function Page() {
  // queryKey 等于 ['/posts', { id: 1 }]
  const { data } = usePost({ variables, suspense: true })

  return (
    <div>
      <div>{data?.title}</div>
      <div>{data?.content}</div>
    </div>
  )
}

console.log(usePost.getKey()) //  ['/posts']
console.log(usePost.getKey(variables)) //  ['/posts', { id: 1 }]

export async function getStaticProps() {
  const queryClient = new QueryClient()

  await queryClient.prefetchQuery(usePost.getKey(variables), usePost.queryFn)

  return {
    props: {
      dehydratedState: dehydrate(queryClient),
    },
  }
}
```

请查阅 [GitHub 上的完整文档](https://github.com/liaoliao666/react-query-kit)。
