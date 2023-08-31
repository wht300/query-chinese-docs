---
id: installation
title: 安装步骤
---

您可以通过[NPM](https://npmjs.com)来安装 React Query，
或者使用老式的 `<script>` 标签通过[unpkg.com](https://unpkg.com)进行引入。

### 使用 NPM

```bash
$ npm i @tanstack/react-query
# 或者
$ pnpm add @tanstack/react-query
# 或者
$ yarn add @tanstack/react-query
```

React Query 兼容 React v16.8+，适用于 ReactDOM 和 React Native。

> 在下载之前想先试试吗？可以尝试一下 [simple](/query/v4/docs/examples/react/simple) 或者 [basic](/query/v4/docs/examples/react/basic) 示例！

### 使用 CDN

如果您不使用模块打包工具或者包管理器，我们还在 [unpkg.com](https://unpkg.com) CDN 上提供了一个全局 ("UMD") 版本。只需将以下 `<script>` 标签添加到您的 HTML 文件底部：

```html
<script src="https://unpkg.com/@tanstack/react-query@4/build/umd/index.production.js"></script>
```

添加完毕后，您就可以访问 `window.ReactQuery` 对象及其导出内容。

> 这种安装/使用方式还需要将 [React CDN 脚本包](https://reactjs.org/docs/cdn-links.html) 也放在页面上。

### 要求

React Query 针对现代浏览器进行了优化。它与以下浏览器配置兼容

```
Chrome >= 73
Firefox >= 78
Edge >= 79
Safari >= 12.1
iOS >= 12.2
opera >= 53
```

> 根据您的环境，您可能需要添加一些 polyfills。如果要支持旧版本浏览器，需要自行从 `node_modules` 对库进行转译。

### 推荐内容

推荐还使用我们的 [ESLint Plugin Query](./eslint/eslint-plugin-query)，以帮助您在编码时捕获错误和不一致。您可以通过以下命令进行安装：

```bash
$ npm i -D @tanstack/eslint-plugin-query
# 或者
$ pnpm add -D @tanstack/eslint-plugin-query
# 或者
$ yarn add -D @tanstack/eslint-plugin-query
```
