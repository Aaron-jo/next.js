---
description: Get started with Next.js in the official documentation, and learn more about all our features!
---

# 快速开始

欢迎阅读 Next.js 文档！

如果你对 Next.js 还不熟悉，我们建议你从[基础教程](https://nextjs.org/learn/basics/getting-started)开始。

通过这些带小测验的互动课程你将学到使用 Next.js 所需的全部知识。

如果你有任何与 Next.js 相关的问题，欢迎随时在 [GitHub Discussions](https://github.com/vercel/next.js/discussions) 上向我们的社区寻求帮助。

#### 操作环境需求

- [Node.js 10.13](https://nodejs.org/) 或更高版本
- MacOS、 Windows (包括 WSL) 和 Linux 都支持

## 安装，设置

我们建议使用 `create-next-app` 创建新的 Next.js 应用程序，它会自动为你设置所有内容。创建项目，请运行:

```bash
npx create-next-app
# or
yarn create next-app
```

安装完成后，按照指示启动开发服务器。试着编辑 `pages/index.js` 文件并在你的浏览器上查看结果。

有关如何使用 `create-next-app` 的更多信息，你可以查看 [`create-next-app` 文档](/docs/create-next-app.md)。

## 手动设置

在你的项目中安装 `next`、`react` 和 `react-dom`:

```bash
npm install next react react-dom
```

打开 `package.json` 文件并如下所示添加 `scripts`:

```json
"scripts": {
  "dev": "next dev",
  "build": "next build",
  "start": "next start"
}
```

这些脚本涉及开发应用程序的不同阶段:

- `dev` - 运行 [`next dev`](/docs/api-reference/cli#development)，在开发模式下启动 Next.js
- `build` - 运行 [`next build`](/docs/api-reference/cli#build)，生成用于生产环境的应用程序
- `start` - 运行 [`next start`](/docs/api-reference/cli#production)，启动 Next.js 生产环境服务器

Next.js 是围绕 [pages](/docs/basic-features/pages.md) 的概念构建的。页面是从 `pages` 目录中的 `.js`、`.jsx`、`.ts` 或者 `.tsx` 文件导出的 [React 组件](https://reactjs.org/docs/components-and-props.html)。

页面根据其文件名与路由关联。例如，`pages/about.js` 被映射到 `/about`。你甚至可以在文件名中添加动态路由参数。

在你的项目中创建一个 `pages` 目录。

为 `./pages/index.js` 文件填充如下内容:

```jsx
function HomePage() {
  return <div>Welcome to Next.js!</div>
}

export default HomePage
```

要开始开发应用程序，请运行 `npm run dev`。这将在 `http://localhost:3000` 上启动开发服务器。

访问 `http://localhost:3000` 即可查看你的应用程序了。

目前为止我们学到了:

- 自动编译和打包 (通过 webpack 和 babel)
- [React 快速刷新](https://nextjs.org/blog/next-9-4#fast-refresh)
- [`./pages/`](/docs/basic-features/pages.md) 中的 [静态生成和服务器端渲染](/docs/basic-features/data-fetching.md)
- [静态文件服务](/docs/basic-features/static-file-serving.md)。 `./public/` 被映射到 `/`

此外，任何 Next.js 应用程序从一开始就是可以投入到生产环境中的，请在我们的[部署文档](/docs/deployment.md)中阅读更多内容。

## 相关内容

有关下一步要做什么，我们推荐以下部分:

<div class="card">
  <a href="/docs/basic-features/pages.md">
    <b>Pages:</b>
    <small>在Next.js中了解更多关于哪些 pages 的信息。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/basic-features/built-in-css-support.md">
    <b>CSS 支持:</b>
    <small>使用内置的 CSS 支持向你的应用程序添加自定义样式。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/api-reference/cli.md">
    <b>CLI:</b>
    <small>了解更多有关 Next.js CLI 的信息。</small>
  </a>
</div>
