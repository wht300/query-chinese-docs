---
id: useQueryErrorResetBoundary
title: useQueryErrorResetBoundary
---

这个钩子将在最近的 `QueryErrorResetBoundary` 内部重置任何查询错误。如果没有定义边界，它将在全局范围内重置它们：

```tsx
import { useQueryErrorResetBoundary } from '@tanstack/react-query'
import { ErrorBoundary } from 'react-error-boundary'

const App: React.FC = () => {
  const { reset } = useQueryErrorResetBoundary()
  return (
    <ErrorBoundary
      onReset={reset}
      fallbackRender={({ resetErrorBoundary }) => (
        <div>
          出现了错误！
          <Button onClick={() => resetErrorBoundary()}>重试</Button>
        </div>
      )}
    >
      <Page />
    </ErrorBoundary>
  )
}
```
