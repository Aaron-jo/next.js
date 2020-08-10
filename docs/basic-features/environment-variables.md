---
description: 学习如何在 Next.js 应用程序中添加及获取环境变量。
---

# 环境变量

> 本文档适用于 9.4 和更高版本的 Next.js。如果你使用的是较旧版本的 Next.js，请参阅[next.config.js 中的环境变量](/docs/api-reference/next.config.js/environment-variables.md)。

<details open>
  <summary><b>示例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/environment-variables">Environment Variables</a></li>
  </ul>
</details>

Next.js 内置了对环境变量的支持，让你可以执行以下操作:

- [使用 `.env.local` 加载环境变量](#loading-environment-variables)
- [向浏览器公开环境变量](#exposing-environment-variables-to-the-browser)

## 加载环境变量

Next.js 内置了对从 `.env.local` 装入 `process.env` 环境变量的支持。

一个 `.env.local` 的例子:

```bash
DB_HOST=localhost
DB_USER=myuser
DB_PASS=mypassword
```

加载`process.env.DB_HOST`、 `process.env.DB_USER` 和 `process.env.DB_PASS` 进入 Node.js 环境，允许您在 [Next.js 数据获取方法](/docs/basic-features/data-fetching)和 [API 路由](/docs/api-routes/introduction)中使用它们。

比如，使用 [`getStaticProps`](/docs/basic-features/data-fetching#getstaticprops-static-generation):

```js
// pages/index.js
export async function getStaticProps() {
  const db = await myDB.connect({
    host: process.env.DB_HOST,
    username: process.env.DB_USER,
    password: process.env.DB_PASS,
  })
  // ...
}
```

> **注意**: Next.js将在您的 `.env*` 文件中自动展开变量（`$VAR`）。
> 这允许你引用其他内置变量，比如:
>
> ```bash
> # .env
> HOSTNAME=localhost
> PORT=8080
> HOST=http://$HOSTNAME:$PORT
> ```
>
> 如果您试图在实际值中使用一个带有 `$` 的变量，那么就需要像这样进行转义 `\$`.
>
> 比如:
>
> ```bash
> # .env
> A=abc
> WRONG=pre$A # becomes "preabc"
> CORRECT=pre\$A # becomes "pre$A"
> ```

## 向浏览器公开环境变量

默认情况下，通过 `.env.local` 加载的所有环境变量仅在 Node.js 环境中可用，这意味着它们不会暴露在浏览器中。

为了向浏览器公开一个变量，你必须在变量前面加上 `NEXT_PUBLIC_` 。例如:

```bash
NEXT_PUBLIC_ANALYTICS_ID=abcdefghijk
```

这个加载过程。自动地将 `process.env.NEXT_PUBLIC_ANALYTICS_ID` 放入Node.js环境中。允许您在代码中的任何位置使用它。由于 `NEXT_PUBLIC_` 前缀，该值将同步发送到浏览器的 JavaScript 中。

```js
// pages/index.js
import setupAnalyticsService from '../lib/my-analytics-service'

// NEXT_PUBLIC_ANALYTICS_ID can be used here as it's prefixed by NEXT_PUBLIC_
setupAnalyticsService(process.env.NEXT_PUBLIC_ANALYTICS_ID)

function HomePage() {
  return <h1>Hello World</h1>
}

export default HomePage
```

## 默认环境变量

一般只需要一个 `.env.local` 文件。但是，有时您可能希望为`development` (`next dev`) 或 `production` (`next start`) 环境添加一些默认值。

Next.js 允许您在 `.env`（所有环境）、`.env.development`（开发环境）和 `.env.production`（生产环境）中设置默认值。

`.env.local` 总是覆盖默认值设置。

> **注意**: `.env`、 `.env.development` 和 `.env.production` 文件应该包含在存储库中，因为它们定义了默认值。**`.env*.local` 应该添加到 `.gitignore`**, 因为这些文件是要忽略的。 `.env.local` 是可以储存秘密的地方。

## Vercel 上的环境变量

当在 [Vercel](https://vercel.com) 上进行部署时，您可以在 Vercel dashboard 中的[环境变量](https://vercel.com/docs/v2/build-step#environment-variables)部分配置秘密。

您仍然可以使用 `.env`、`.env.development` 和 `.env.production` 来添加默认值。

如果您已经配置了[开发环境变量](https://vercel.com/docs/v2/build-step#development-environment-variables)，您可以使用以下命令将它们拉入 `.env.local` 以便在本地机器上使用：

```bash
vercel env pull .env.local
```

## 测试环境变量

除了 `development` 和 `production` 环境之外，还有第三个备选办法：`test`。与您可以为开发或生产环境设置默认值的方式一样，您也可以为测试环境设置 `.env.test` 文件（尽管这个文件不如前两个文件那么常见）。

当使用诸如 `jest` 或 `cypress` 之类的工具运行测试时，这一点非常有用，因为您需要为测试目的设置特定的环境变量。如果 `NODE_ENV` 被设置为 `test` ，测试默认值将被加载，尽管您通常不需要手动执行此操作，因为测试工具将为您解决此问题。

`test` 与 `development`（和`production`）之间有一个很小的区别，即您需要记住：`.env.local` 不会加载，因为您期望测试对每个人都产生相同的结果。通过这种方式，每个测试执行将在不同的执行过程中使用相同的 env 默认值，方法是忽略您的`.env.local`（其目的是覆盖默认设置）。

> **注意**: 与默认环境变量类似，`.env.test` 文件应该包含在存储库中，但是`.env.test.local`不应该包含，因为 `.env*.local` 应该通过 `.gitignore` 设置忽略。
