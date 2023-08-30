---
id: eslint-plugin-query
title: ESLint Plugin Query
---

TanStack Query 自带了一个专属的 ESLint 插件。该插件用于强制实施最佳实践，帮助您避免常见的错误。

## 安装

这个插件是一个独立的包，您需要安装它：

```bash
$ npm i -D @tanstack/eslint-plugin-query
# 或者
$ pnpm add -D @tanstack/eslint-plugin-query
# 或者
$ yarn add -D @tanstack/eslint-plugin-query
```

## 使用

将 `@tanstack/eslint-plugin-query` 添加到您的 `.eslintrc` 配置文件的插件部分：

```json
{
  "plugins": ["@tanstack/query"]
}
```

然后在规则部分配置您想要使用的规则：

```json
{
  "rules": {
    "@tanstack/query/exhaustive-deps": "error",
    "@tanstack/query/prefer-query-object-syntax": "error"
  }
}
```

### 推荐配置

您还可以启用我们插件的所有推荐规则。在 `extends` 中添加 `plugin:@tanstack/eslint-plugin-query/recommended`：

```json
{
  "extends": ["plugin:@tanstack/eslint-plugin-query/recommended"]
}
```
