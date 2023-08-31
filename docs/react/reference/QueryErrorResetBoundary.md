---
id: QueryErrorResetBoundary
title: 查询错误重置边界
---

当在你的查询中使用**悬挂**（suspense）或**useErrorBoundaries**时，你需要一种方法来让查询知道在重新渲染后要尝试再次请求，以解决一些错误。通过 `QueryErrorResetBoundary` 组件，你可以在组件边界内重置任何查询错误。

```tsx
import { QueryErrorResetBoundary } from '@tanstack/react-query'
import { ErrorBoundary } from 'react-error-boundary'

const App: React.FC = () => (
  <QueryErrorResetBoundary>
    {({ reset }) => (
      <ErrorBoundary
        onReset={reset}
        fallbackRender={({ resetErrorBoundary }) => (
          <div>
            出现了一个错误！
            <Button onClick={() => resetErrorBoundary()}>重试</Button>
          </div>
        )}
      >
        <Page />
      </ErrorBoundary>
    )}
  </QueryErrorResetBoundary>
)
```
