---
description: 浏览器支持以及 Next.js 支持哪些 JavaScript 特性。
---

# 支持的浏览器和特性

Next.js 不需要配置就能支持 **IE11 和所有现代浏览器** (Edge, Firefox, Chrome, Safari, Opera, 等)。

## 补丁

我们透明地注入 IE11 兼容性所需的补丁。此外，我们还注入了广泛使用的补丁，包括:

- [**fetch()**](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) — 替换: `whatwg-fetch` 和 `unfetch`。
- [**URL**](https://developer.mozilla.org/en-US/docs/Web/API/URL) — 替换: the [`url` package (Node.js API)](https://nodejs.org/api/url.html).
- [**Object.assign()**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) — 替换: `object-assign`、 `object.assign` 和 `core-js/object/assign`。

如果您的任何依赖项包含这些补丁，它们将自动从生产构建中移除，以避免重复。

此外，为了减少编译产物的大小，Next.js 将只为需要它们的浏览器加载这些补丁。全球大部分的网络流量不会下载这些补丁。

### 服务端补丁

除了客户端上的 `fetch()` 之外，在 Node.js 环境中，Next.js 也为 `fetch()` 添加了补丁。您可以在服务器代码上使用 `fetch()` ，而不必使用诸如 `isomorphic-unfetch `或 `node-fetch` 之类的词。

### 自定义补丁

如果你自己的代码或任何外部代码 npm 依赖项需要目标浏览器不支持的特性，您需要自己添加补丁。

在这种情况下，您应该添加**特定补丁**，您需要在[自定义 `<App>`](docs/advanced-features/custom-app.md) 或单个组件中在顶部添加导入。

## JavaScript 语言特性

Next.js 允许您使用最新的 JavaScript 特性。除[ES6 特性](https://github.com/lukehoban/es6features)外，Next.js 还支持:

- [Async/await](https://github.com/tc39/ecmascript-asyncawait) (ES2017)
- [Object Rest/Spread Properties](https://github.com/tc39/proposal-object-rest-spread) (ES2018)
- [Dynamic `import()`](https://github.com/tc39/proposal-dynamic-import) (ES2020)
- [Optional Chaining](https://github.com/tc39/proposal-optional-chaining) (ES2020)
- [Nullish Coalescing](https://github.com/tc39/proposal-nullish-coalescing) (ES2020)
- [Class Fields](https://github.com/tc39/proposal-class-fields) and [Static Properties](https://github.com/tc39/proposal-static-class-features) (part of stage 3 proposal)
- 还有更多!

### TypeScript 特性

Next.js 提供了内置的TypeScript支持。 [在这里学习更多](/docs/basic-features/typescript.md)。

### 自定义 Babel 配置 (Advanced)

您可以自定义babel配置。 [在这里学习更多](/docs/advanced-features/customizing-babel-config.md)。
