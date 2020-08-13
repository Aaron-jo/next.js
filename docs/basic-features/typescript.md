---
description: Next.js 默认支持 TypeScript，并具有针对页面和 API 的内置类型。 在这里，您可以从 TypeScript 开始 Next.js。
---

# TypeScript

<details>
  <summary><b>示例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-typescript">TypeScript</a></li>
  </ul>
</details>

Next.js 提供了一种开箱即用的集成 [TypeScript](https://www.typescriptlang.org/) 体验，类似于 IDE。

首先，在项目的根目录中创建一个空的 `tsconfig.json` 文件:

```bash
touch tsconfig.json
```

Next.js 将使用默认值自动配置此文件。还支持使用自定义[编译器选项](https://www.typescriptlang.org/docs/handbook/compiler-options.html)提供自己的 `tsconfig.json` 。

> Next.js 使用 Babel 来处理 TypeScript，它有一些[警告](https://babeljs.io/docs/en/babel-plugin-transform-typescript#caveats)，一些[编译器选项的处理方式也不同](https://babeljs.io/docs/en/babel-plugin-transform-typescript#typescript-compiler-options)。

然后，运行 `next`（正常情况下 `npm run dev`）和 Next.js 将指导您安装所需的包，以完成安装工作:

```bash
npm run dev

# You'll see instructions like these:
#
# Please install typescript, @types/react, and @types/node by running:
#
#         yarn add --dev typescript @types/react @types/node
#
# ...
```

现在可以开始将文件从 `.js` 转换为 `.tsx` 并利用 TypeScript 的好处！。

> 将自动在您的项目的根目录中创建 `next-env.d.ts` 文件。这个文件确保 TypeScript 编译器检测到 Next.js 类型。 **你不能删除它**，但是，你可以编辑它 (但你不需要这么做)。

> 默认情况下 Next.js `strict` 模式是关闭的。当你对 TypeScript 足够熟悉，建议你在 `tsconfig.json` 中打开它。

默认情况下，`next build` 时 Next.js 将执行类型检查。我们建议在开发过程中使用代码编辑器类型检查。

如果您想让错误报告沉默，请参考文档中的[忽略 TypeScript 错误](/docs/api-reference/next.config.js/ignoring-typescript-errors.md)。

## 静态生成与服务器端渲染

关于 `getStaticProps`、`getStaticPaths` 和 `getServerSideProps`，你可以分别使用这些类型 `GetStaticProps` 、 `GetStaticPaths` 和 `GetServerSideProps`:

```ts
import { GetStaticProps, GetStaticPaths, GetServerSideProps } from 'next'

export const getStaticProps: GetStaticProps = async (context) => {
  // ...
}

export const getStaticPaths: GetStaticPaths = async () => {
  // ...
}

export const getServerSideProps: GetServerSideProps = async (context) => {
  // ...
}
```

> 如果您使用的是 `getInitialProps`，您可以[按照本页的说明](/docs/api-reference/data-fetching/getInitialProps.md#typescript)。

## API Routes

以下是如何将内置类型用于 API routes的示例:

```ts
import { NextApiRequest, NextApiResponse } from 'next'

export default (req: NextApiRequest, res: NextApiResponse) => {
  res.status(200).json({ name: 'John Doe' })
}
```

您还可以定义响应数据类型:

```ts
import { NextApiRequest, NextApiResponse } from 'next'

type Data = {
  name: string
}

export default (req: NextApiRequest, res: NextApiResponse<Data>) => {
  res.status(200).json({ name: 'John Doe' })
}
```

## 自定义 `App`

如果您使用[自定义 `App`](/docs/advanced-features/custom-app)，您可以在 `./pages/_app.tsx` 中像这样使用内置类型 `AppProps` :

```ts
// import App from "next/app";
import type { AppProps /*, AppContext */ } from 'next/app'

function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />
}

// Only uncomment this method if you have blocking data requirements for
// every single page in your application. This disables the ability to
// perform automatic static optimization, causing every page in your app to
// be server-side rendered.
//
// MyApp.getInitialProps = async (appContext: AppContext) => {
//   // calls page's `getInitialProps` and fills `appProps.pageProps`
//   const appProps = await App.getInitialProps(appContext);

//   return { ...appProps }
// }

export default MyApp
```

## 路径别名和 baseUrl

Next.js 自动支持 `tsconfig.json` `"paths"` 和 `"baseUrl"` 选项。

您可以在[模块路径别名文档](/docs/advanced-features/module-path-aliases.md)中了解更多有关此功能的信息。
