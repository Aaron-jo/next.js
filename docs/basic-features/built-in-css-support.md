---
description: Next.js 支持全局 CSS 文件或 CSS 模块、基于 `styled-jsx` 的 CSS-in-JS 或任何其他 CSS-in-JS 解决方案！请在这里了解更多细节。
---

# 内置 CSS 支持

<details open>
  <summary><b>示例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/basic-css">Basic CSS Example</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-tailwindcss">With Tailwind CSS</a></li>
  </ul>
</details>

Next.js 允许你在 JavaScript 文件中导入 CSS 文件。
因为 Next.js 扩展了 JavaScript 中的 [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 的概念。

## 添加全局样式表

要将样式表添加到你的应用程序中，请在 `pages/_app.js` 文件中导入（import）CSS 文件。

例如，假设有以下样式表 `styles.css`:

```css
body {
  font-family: 'SF Pro Text', 'SF Pro Icons', 'Helvetica Neue', 'Helvetica',
    'Arial', sans-serif;
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```

创建一个 [`pages/_app.js` file](/docs/advanced-features/custom-app) 文件，如果它不存在的话。
然后 [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 该 `styles.css` 文件。

```jsx
import '../styles.css'

// This default export is required in a new `pages/_app.js` file.
export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

这些样式 (`styles.css`) 将应用于你的应用程序中的所有页面和组件。
由于样式表的全局特性，为了避免冲突，你应该**只在 [`pages/_app.js`](/docs/advanced-features/custom-app) 文件中导入样式表**。

在开发中，以这种方式表示样式可让你在编辑样式时对其进行热重载，这意味着你可以保持应用程序的状态。

在生产环境中，所有 CSS 文件将自动合并为一个经过精简的 `.css` 文件。

### 从 `node_modules` 导入样式

如果你想从 `node_modules` 导入 CSS 文件，你必须在 `pages/_app.js` 中进行。

## 添加组件级 CSS

Next.js 通过 `[name].module.css` 文件命名约定来支持 [CSS Modules](https://github.com/css-modules/css-modules) 。

CSS Modules 通过自动创建唯一的类名从而将 CSS 限定在局部范围内。
这使你可以在不同文件中使用相同的 CSS 类名，而不必担心冲突。

此行为使 CSS Module 成为包含组件级 CSS 的理想方法。
CSS Module 文件**可以导入到应用程序中的任何位置**。

例如，假设 `components/` 目录下有一个可重用 `Button` 组件:

首先，创建 `components/Button.module.css` 文件并填入以下内容:

```css
/*
You do not need to worry about .error {} colliding with any other `.css` or
`.module.css` files!
*/
.error {
  color: white;
  background-color: red;
}
```

然后，创建 `components/Button.js` 文件，导入并使用上述 CSS 文件:

```jsx
import styles from './Button.module.css'

export function Button() {
  return (
    <button
      type="button"
      // Note how the "error" class is accessed as a property on the imported
      // `styles` object.
      className={styles.error}
    >
      Destroy
    </button>
  )
}
```

CSS Modules 是一项_可选功能_，**仅对带有 `.module.css` 扩展名的文件启用**。
普通的 `<link>` 样式表和全部 CSS 文件仍然是被支持的。

在生产环境中，所有 CSS Module 文件将被自动合并为**多个经过精简和代码分割的** `.css` 文件。
这些 `.css` 文件代表应用程序中的热执行路径，从而确保为应用程序绘制页面加载所需的最少的 CSS。

## Sass 支持

Next.js 允许你导入具有 `.scss` 和 `.sass` 扩展名的 Sass 文件。
你可以通过 CSS Modules以及 `.module.scss` 或 `.module.sass` 扩展名来使用组件级的 Sass。

在使用 Next.js 的内置 Sass 支持前，请确保安装了 [`sass`](https://github.com/sass/sass):

```bash
npm install sass
```

对 Sass 的支持与上述内置 CSS 的支持具有相同的好处和限制。
Sass support has the same benefits and restrictions as the built-in CSS support detailed above.

> **注意**: Sass支持[两个不同的语法](https://sass-lang.com/documentation/syntax)，每个都有自己的扩展名。
> `.scss` 扩展需要你使用[SCSS语法](https://sass-lang.com/documentation/syntax#scss)。
> 而 `.sass` 扩展名要求你使用[缩进语法 ("Sass")](https://sass-lang.com/documentation/syntax#the-indented-syntax)。
>
> 如果你不确定选择哪一个，请从 `.scss` 扩展开始，它是CSS的超集，不需要学习缩进语法 ("Sass")。

### 自定义 Sass 参数

如果要配置 Sass 编译器，可以使用 `next.config.js` 文件中的 `sassOptions` 参数进行配置。

例如，添加 `includePaths`:

```js
const path = require('path')

module.exports = {
  sassOptions: {
    includePaths: [path.join(__dirname, 'styles')],
  },
}
```

## 对 Less 和 Stylus 的支持

如需支持 `.less` 或 `.styl` 文件，可以使用如下插件:

- [@zeit/next-less](https://github.com/vercel/next-plugins/tree/master/packages/next-less)
- [@zeit/next-stylus](https://github.com/vercel/next-plugins/tree/master/packages/next-stylus)

如果使用 less 插件，请不要忘记添加 less 依赖，否则将看到类似以下的错误:

```bash
Error: Cannot find module 'less'
```

## CSS-in-JS

<details>
  <summary><b>示例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-styled-jsx">Styled JSX</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-styled-components">Styled Components</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-emotion">Emotion</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-tailwindcss-emotion">Tailwind CSS + Emotion</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-styletron">Styletron</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-glamor">Glamor</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-cxs">Cxs</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-aphrodite">Aphrodite</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-fela">Fela</a></li>
  </ul>
</details>

可以使用任何现有的 CSS-in-JS 解决方案。
最简单的一种是内联样式:

```jsx
function HiThere() {
  return <p style={{ color: 'red' }}>hi there</p>
}

export default HiThere
```

我们包含了 [styled-jsx](https://github.com/vercel/styled-jsx) 以支持范围隔离的 CSS。
目的是支持类似于 Web 组件的 "shadow CSS"，但不幸的是[不支持服务器端渲染且仅支持 JS](https://github.com/w3c/webcomponents/issues/71)。

有关其他流行的 CSS-in-JS 解决方案（如样式化组件），请参见以上示例。

使用 `styled-jsx` 的组件看起来像这样:

```jsx
function HelloWorld() {
  return (
    <div>
      Hello world
      <p>scoped!</p>
      <style jsx>{`
        p {
          color: blue;
        }
        div {
          background: red;
        }
        @media (max-width: 600px) {
          div {
            background: blue;
          }
        }
      `}</style>
      <style global jsx>{`
        body {
          background: black;
        }
      `}</style>
    </div>
  )
}

export default HelloWorld
```

请参阅[styled-jsx 文档](https://github.com/vercel/styled-jsx)以获取更多示例。

## FAQ

### 它是否在禁用JavaScript的情况下工作?

是的，如果禁用了 JavaScript，CSS 仍然会被加载到产品构建中(`next start`)。在开发过程中，我们需要启用 JavaScript，以提供最佳的开发体验与[快速刷新](https://nextjs.org/blog/next-9-4#fast-refresh)。

## 相关内容

以下是我们建议接下来需要学习的内容:

<div class="card">
  <a href="/docs/advanced-features/customizing-postcss-config.md">
    <b>自定义 PostCSS 配置:</b>
    <small>用你自己的插件扩展由 Next.js 添加的 PostCSS 配置和插件。</small>
  </a>
</div>
