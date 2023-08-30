---
id: lukemorales-query-key-factory
title: Query Key Factory
---

使用自动完成功能的类型安全查询键管理。专注于编写和使查询失效，而无需记住如何为特定查询设置键！

## 安装
你可以通过 [NPM](https://www.npmjs.com/package/@lukemorales/query-key-factory) 安装 Query Key Factory。

```bash
$ npm i @lukemorales/query-key-factory
# 或者
$ pnpm add @lukemorales/query-key-factory
# 或者
$ yarn add @lukemorales/query-key-factory
```

## 快速开始
首先，定义你的应用程序的查询键：

### 在单个文件中声明所有内容
```tsx
import { createQueryKeyStore } from '@lukemorales/query-key-factory'

export const queryKeys = createQueryKeyStore({
  users: null,
  todos: {
    detail: (todoId: string) => [todoId],
    list: (filters: TodoFilters) => ({
      queryKey: [{ filters }],
      queryFn: (ctx) => api.getTodos({ filters, page: ctx.pageParam }),
    }),
  },
})
```

### 按功能进行精细声明
```tsx
import { createQueryKeys, mergeQueryKeys } from '@lukemorales/query-key-factory'

// my-api/users.ts
export const usersKeys = createQueryKeys('users')

// my-api/todos.ts
export const todosKeys = createQueryKeys('todos', {
  detail: (todoId: string) => [todoId],
  list: (filters: TodoFilters) => ({
    queryKey: [{ filters }],
    queryFn: (ctx) => api.getTodos({ filters, page: ctx.pageParam }),
  }),
})

// my-api/index.ts
export const queryKeys = mergeQueryKeys(usersKeys, todosKeys)
```

在整个代码库中使用它作为缓存管理的查询键的唯一来源：
```tsx
import { queryKeys, completeTodo, fetchSingleTodo } from '../my-api'

export function Todo({ todoId }) {
  const queryClient = useQueryClient()

  const query = useQuery(queryKeys.todos.detail(todoId))

  const mutation = useMutation({
    mutationFn: completeTodo,
    onSuccess: () => {
      // 使查询失效并重新获取数据
      queryClient.invalidateQueries({ queryKey: queryKeys.todos.list.queryKey })
    },
  })

  return (
    <div>
      <h1>
        {query.data?.title}
      </h1>

      <p>
        {query.data?.description}
      </p>

      <button
        onClick={() => {
          mutation.mutate({ todoId })
        }}
      >
        完成任务
      </button>
    </div>
  )
}
```

请查阅 [GitHub 上的完整文档](https://github.com/lukemorales/query-key-factory)。
