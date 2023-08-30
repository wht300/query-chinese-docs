---
id: testing
title: 测试
---

React Query 通过钩子（hooks）工作 - 可以是我们提供的钩子，也可以是包裹在它们周围的自定义钩子。

对于 React 17 或更早版本，可以使用 [React Hooks Testing Library](https://react-hooks-testing-library.com/) 库来编写这些自定义钩子的单元测试。

通过以下命令安装该库：

```sh
npm install @testing-library/react-hooks react-test-renderer --save-dev
```

（`react-test-renderer` 库是 `@testing-library/react-hooks` 的对等依赖项，需要与你使用的 React 版本相对应。）

*注意*：在使用 React 18 或更高版本时，`renderHook` 可以直接通过 `@testing-library/react` 包访问，无需再使用 `@testing-library/react-hooks`。

## 我们的第一个测试

安装完成后，可以编写一个简单的测试。假设有以下自定义钩子：

```tsx
export function useCustomHook() {
  return useQuery({ queryKey: ['customHook'], queryFn: () => 'Hello' });
}
```

在使用 React 17 或更早版本时，我们可以如下编写测试：

```tsx
const queryClient = new QueryClient();
const wrapper = ({ children }) => (
  <QueryClientProvider client={queryClient}>
    {children}
  </QueryClientProvider>
);

const { result, waitFor } = renderHook(() => useCustomHook(), { wrapper });

await waitFor(() => result.current.isSuccess);

expect(result.current.data).toEqual("Hello");
```

在使用 React 18 或更高版本时，`waitFor` 的语义发生了变化，上述测试需要修改如下：

```tsx
import { renderHook, waitFor } from "@testing-library/react";

...

const { result } = renderHook(() => useCustomHook(), { wrapper });

await waitFor(() => expect(result.current.isSuccess).toBe(true));
```

注意，我们提供了一个自定义的包装器，用于构建 `QueryClient` 和 `QueryClientProvider`。这有助于确保我们的测试与其他测试完全隔离。

可以只编写这个包装器一次，但如果这样做，我们需要确保在每个测试之前清除 `QueryClient`，并且测试不要并行运行，否则一个测试会影响其他测试的结果。

## 关闭重试

该库默认进行三次指数退避的重试，这意味着如果你想测试一个错误的查询，你的测试很可能会超时。最简单的关闭重试的方法是通过 `QueryClientProvider`。让我们扩展上面的示例：

```tsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      // ✅ 关闭重试
      retry: false,
    },
  },
})
const wrapper = ({ children }) => (
  <QueryClientProvider client={queryClient}>
    {children}
  </QueryClientProvider>
);
```

这将为组件树中的所有查询设置默认值为“无重试”。重要的是要知道，只有当你的实际 `useQuery` 没有显式设置重试时，这才起作用。如果有一个要求进行 5 次重试的查询，这仍然会优先，因为默认值仅作为后备采用。

## 关闭网络错误日志记录

在测试时，我们希望禁止将网络错误记录到控制台中。为此，我们可以将自定义日志记录器传递给 `QueryClient`：

```tsx
import { QueryClient } from '@tanstack/react-query'

const queryClient = new QueryClient({
  logger: {
    log: console.log,
    warn: console.warn,
    // ✅ 在测试中不再在控制台上显示错误
    error: process.env.NODE_ENV === 'test' ? () => {} : console.error,
  },
})
```

## 在 Jest 中将 cacheTime 设置为 Infinity

如果你使用 Jest，可以将 `cacheTime` 设置为 `Infinity`，以避免出现“Jest 在测试运行完成后一秒钟后仍未退出”的错误消息。这是服务器的默认行为，只有在你显式设置了 `cacheTime` 的情况下才需要设置。

## 测试网络调用

React Query 的主要用途是缓存网络请求，因此测试代码是否正确发出网络请求非常重要。

有很多测试这些的方法，但是在本例中，我们将使用 [nock](https://www.npmjs.com/package/nock)。

假设有以下自定义钩子：

```tsx
function useFetchData() {
  return useQuery({
    queryKey: ['fetchData'],
    queryFn: () => request('/api/data'),
  });
}
```

我们可以如下编写测试：

```tsx
const queryClient = new QueryClient();
const wrapper = ({ children }) => (
  <QueryClientProvider client={queryClient}>
    {children}
  </QueryClientProvider>
);

const expectation = nock('http://example.com')
  .get('/api/data')
  .reply(200, {
    answer: 42
  });

const { result, waitFor } = renderHook(() => useFetchData(), { wrapper });

await waitFor(() => {
  return result.current.isSuccess;
});

expect(result.current.data).toEqual({answer: 42});
```

在这里，我们使用 `waitFor` 并等待查询状态指示请求是否成功。这样我们就知道我们的钩子已经完成并且应该有正确的数据。*注意*：在使用 React 18 时，`waitFor` 的语义已经发生了变化，如上所述。

## 测试加载更多 / 无限滚动

首先，我们需要模拟 API 响应：

```tsx
function generateMockedResponse(page) {
  return {
    page: page,
    items: [...]
  }
}
```

然后，我们的 `nock` 配置需要基于页面区分响应，我们将使用 `uri` 来实现。这里的 `uri` 的值将类似于 `"/?page=1"` 或 `"/?page=2"`。

```tsx
const expectation = nock('http://example.com')
  .persist()
  .query(true)
  .get('/api/data')
  .reply(200, (uri) => {
    const url = new URL(`http://example.com${uri}`);
    const { page } = Object

.fromEntries(url.searchParams);
    return generateMockedResponse(page);
  });
```

（注意 `.persist()`，因为我们将从此端点多次调用）

现在我们可以安全地运行我们的测试，这里的技巧是等待数据断言通过：

```tsx
const { result, waitFor } = renderHook(
  () => useInfiniteQueryCustomHook(),
  { wrapper },
);

await waitFor(() => result.current.isSuccess);

expect(result.current.data.pages).toStrictEqual(generateMockedResponse(1));

result.current.fetchNextPage();

await waitFor(() =>
  expect(result.current.data.pages).toStrictEqual([
    ...generateMockedResponse(1),
    ...generateMockedResponse(2),
  ]),
);

expectation.done();
```

*注意*：在使用 React 18 时，`waitFor` 的语义已经发生了变化，如上所述。

## 进一步阅读

有关额外的提示以及使用 `mock-service-worker` 的备用设置，请查看社区资源中的 [Testing React Query](../community/tkdodos-blog#5-testing-react-query)。
