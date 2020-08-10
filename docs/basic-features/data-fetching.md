---
description: 'Next.js 有两种预渲染的模式: 静态生成和服务端渲染。在这里学习他们是怎样工作的。'
---

# 数据获取

> 本文档适用于 Next.js 9.3 或更高版本。如果你使用的是较旧版本的 Next.js，请参阅[先前版本的文档](https://nextjs.org/docs/tag/v9.2.2/basic-features/pages)。

<details open>
  <summary><b>示例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-wordpress">WordPress Example</a> (<a href="https://next-blog-wordpress.now.sh">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/blog-starter">Blog Starter using markdown files</a> (<a href="https://next-blog-starter.now.sh/">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-datocms">DatoCMS Example</a> (<a href="https://next-blog-datocms.now.sh/">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-takeshape">TakeShape Example</a> (<a href="https://next-blog-takeshape.now.sh/">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-sanity">Sanity Example</a> (<a href="https://next-blog-sanity.now.sh/">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-prismic">Prismic Example</a> (<a href="https://next-blog-prismic.now.sh/">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-contentful">Contentful Example</a> (<a href="https://next-blog-contentful.now.sh/">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-strapi">Strapi Example</a> (<a href="https://next-blog-strapi.now.sh/">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-agilitycms">Agility CMS Example</a> (<a href="https://next-blog-agilitycms.now.sh/">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-cosmic">Cosmic Example</a> (<a href="https://next-blog-cosmic.now.sh/">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-buttercms">ButterCMS Example</a> (<a href="https://next-blog-buttercms.now.sh/">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-storyblok">Storyblok Example</a> (<a href="https://next-blog-storyblok.now.sh/">Demo</a>)</li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/cms-graphcms">GraphCMS Example</a> (<a href="https://next-blog-graphcms.now.sh/">Demo</a>)</li>
    <li><a href="https://static-tweet.now.sh/">Static Tweet Demo</a></li>
  </ul>
</details>

在[页面文档](/docs/basic-features/pages.md)中，我们已经解释了 Next.js 有两种预渲染形式：**静态生成**和**服务端渲染**。在本页中，我们将深入讨论每种情况下的数据获取策略。如果你还没有阅读页面文档，我们建议你[先阅读页面文档](/docs/basic-features/pages.md)。

我们将讨论三个 Next.js 专属的函数，你可以使用这些函数来获取用于预渲染的数据:

- [`getStaticProps`](#getstaticprops-static-generation) (静态生成): 在**构建时**请求数据。
- [`getStaticPaths`](#getstaticpaths-static-generation) (静态生成): 指定[动态路由](/docs/routing/dynamic-routes.md)根据数据预渲染。
- [`getServerSideProps`](#getserversideprops-server-side-rendering) (服务端渲染): 在**每个请求时**请求数据。

此外，我们将简要讨论如何在客户端获取数据。

## `getStaticProps` (静态生成)

如果从一个页面导出一个名为 `getStaticProps`的 `async` 函数，则 Next.js 将在构建时使用由 `getStaticProps` 返回的属性对该页面进行预渲染。

```jsx
export async function getStaticProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  }
}
```

`context`参数是一个包含以下键的对象:

- `params`: 包含使用动态路由的页面的路由参数。例如，如果页面名称为 `[id].js`，那么 `params` 将看起来像 `{ id: ... }`。要了解更多信息，请查看[动态路由文档](/docs/routing/dynamic-routes.md)。你应该把它和 `getStaticPaths` 一起使用，稍后我们会对此进行说明。
- `preview`: 如果页面是预览模式，则`preview`是`true`，否则为`false`。参见[预览模式文档](/docs/advanced-features/preview-mode.md)。
- `previewData`: 包含由 `setPreviewData` 设置的预览数据集。参见[预览模式文档](/docs/advanced-features/preview-mode.md)。

`getStaticProps` 应该返回一个像这样的对象:

- `props` - **必须的**的对象，带有将由页面组件接收的属性。它应该是一个[可序列化的的对象](https://en.wikipedia.org/wiki/Serialization)。
- `revalidate` - 一个**可选**的配置，以秒为单位，可以在此数秒后重新你页面。更多关于[增量静态再生](#incremental-static-regeneration)。

> **注意**: 你可以在顶级作用域中导入模块，以便在 `getStaticProps` 中使用。
> `getStaticProps` 中使用的导入将不仅仅是客户端模块，[说明如下](#write-server-side-code-directly)。

### 简单的例子

下面是一个使用 `getStaticProps` 从 CMS（内容管理系统）中获取博客文章列表的例子。这个例子也出现在[页面文档](/docs/basic-features/pages.md)中。

```jsx
// posts will be populated at build time by getStaticProps()
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// This function gets called at build time on server-side.
// It won't be called on client-side, so you can even do
// direct database queries. See the "Technical details" section.
export async function getStaticProps() {
  // Call an external API endpoint to get posts.
  // You can use any data fetching library
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // By returning { props: posts }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```

### 我什么时候需要用 `getStaticProps` 呢?

你应该使用 `getStaticProps` ，如果:

- 渲染页面所需的数据可在构建时提前于用户的请求提供。
- 数据来自 headless CMS。
- 资料可以公开缓存(并非特定于使用者)。
- 页面必须预渲染（对于SEO）并且速度非常快 - `getStaticProps` 你HTML和JSON文件，这两个文件都可以通过 CDN 缓存来提升性能。

### TypeScript: 使用 `GetStaticProps`

在 TypeScript 中, 你可以使用从 `next` 中导出的 `GetStaticProps` 类型:

```ts
import { GetStaticProps } from 'next'

export const getStaticProps: GetStaticProps = async (context) => {
  // ...
}
```

如果你想得到你的属性的推断类型，你可以使用 `InferGetStaticPropsType<typeof getStaticProps>`，如下所示:

```tsx
import { InferGetStaticPropsType } from 'next'

type Post = {
  author: string
  content: string
}

export const getStaticProps = async () => {
  const res = await fetch('https://.../posts')
  const posts: Post[] = await res.json()

  return {
    props: {
      posts,
    },
  }
}

function Blog({ posts }: InferGetStaticPropsType<typeof getStaticProps>) {
  // will resolve posts to type Post[]
}

export default Blog
```

### 增量静态再生

> 这个特性是在 [Next.js9.5](https://nextjs.org/blog/next-9-5#stable-incremental-static-regeneration) 及更高版本中引入的。如果你使用的是旧版本的 Next.js，请在尝试增量静态再生之前进行升级。

<details open>
  <summary><b>示例</b></summary>
  <ul>
    <li><a href="https://reactions-demo.now.sh/">Static Reactions Demo</a></li>
  </ul>
</details>

使用 [`getStaticProps`](#getstaticprops-static-generation)，你不必停止依赖动态内容，**静态内容也可以是动态的**。增量静态再生允许你通过在后台重新渲染的方式更新_现有的_网页。

受[stale-while-revalidate](https://tools.ietf.org/html/rfc5861)的启发，后台恢复可以确保不间断地从静态存储中获得流量，新构建的页面只有在你完成后才能推送。

参考我们以前的 [`getStaticProps` example](#simple-example)，但现在启用了再生：

```jsx
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// This function gets called at build time on server-side.
// It may be called again, on a serverless function, if
// revalidation is enabled and a new request comes in
export async function getStaticProps() {
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  return {
    props: {
      posts,
    },
    // Next.js will attempt to re-generate the page:
    // - When a request comes in
    // - At most once every second
    revalidate: 1, // In seconds
  }
}

export default Blog
```

现在博客文章列表将每秒重新验证一次；如果你添加一个新的博客文章，它几乎马上就可以使用，而不必重新构建应用程序或进行新的部署。

这个方法与 [`fallback: true`](#fallback-true) 完全匹配。因为现在你可以有一个最新的文章列表，并且有一个[博客文章页面](#fallback-pages)，可以根据需要你博客文章，无论你添加或更新了多少文章。

#### 规划静态内容

与传统的 SSR 不同, [增量静态再生](#incremental-static-regeneration)确保你保留静态的好处:

- 没有延迟高峰。页面提供的速度始终很快
- 页面永远不会脱机。如果背景页重新你失败，旧页将保持不变
- 低数据库和后端负载。页面最多同时重新计算一次

### 读取文件: 使用 `process.cwd()`

可以直接从 `getStaticProps` 中读取文件。

为了做到这一点，你必须获得一个文件的完整路径。

因为 Next.js 将你的代码编译到一个单独的目录中，所以你不能使用 `__dirname` 作为路径，它将返回的路径与页面目录不同。

相反，你可以使用 `process.cwd()`，它为你提供了执行 Next.js 的目录。

```jsx
import fs from 'fs'
import path from 'path'

// posts will be populated at build time by getStaticProps()
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>
          <h3>{post.filename}</h3>
          <p>{post.content}</p>
        </li>
      ))}
    </ul>
  )
}

// This function gets called at build time on server-side.
// It won't be called on client-side, so you can even do
// direct database queries. See the "Technical details" section.
export async function getStaticProps() {
  const postsDirectory = path.join(process.cwd(), 'posts')
  const filenames = fs.readdirSync(postsDirectory)

  const posts = filenames.map((filename) => {
    const filePath = path.join(postsDirectory, filename)
    const fileContents = fs.readFileSync(filePath, 'utf8')

    // Generally you would parse/transform the contents
    // For example you can transform markdown to HTML here

    return {
      filename,
      content: fileContents,
    }
  })
  // By returning { props: posts }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```

### 技术技巧

#### 仅在构建时运行

因为 `getStaticProps` 在构建时运行，所以它在你静态的 HTML时，**不会**接收仅在请求时可用的数据，如查询参数或 HTTP headers。

#### 直接编写服务端代码

请注意，`getStaticProps`仅在**服务器端运行**。它永远不会在客户端运行。它甚至不会包含在浏览器的 JS 包中。这意味着你可以编写直接数据库查询之类的代码，而不必将它们发送到浏览器。你不应该从 `getStaticProps` 获取 **API route**。 — 相反，你可以直接在 `getStaticProps` 中编写服务器端代码。

可以使用 [这个工具](https://next-code-elimination.now.sh/)来验证 Next.js 从客户端编译包中消除了什么。

#### 静态生成 HTML 和 JSONS

当构建时，除了页面之外，在构建时预渲染带有 `getStaticProps` 的页面 HTML 文件，Next.js 还会生成一个保存运行 `getStaticProps` 的结果JSON文件。

这个 JSON 文件将通过 `next/link`（[文档](/docs/api-reference/next/link.md)）或 `next/router`（[文档](/docs/api-reference/next/router.md)）在客户端路由中使用。当你导航到一个使用 `getStaticProps` 预渲染的页面时，Next.js将获取这个 JSON 文件（在构建时预先计算），并将其用作页面组件的属性。这意味着客户端页面转换将仅使用导出的 JSON 而不调用 `getStaticProps`。

#### 只允许出现在页面组件

`getStaticProps` 只能从**页面**导出，不能从非页面文件导出。

这种限制的原因之一是 React 需要在渲染页面之前拥有所有必需的数据。

此外，你必须使用 `export async function getStaticProps() {}` - 如果你把 `getStaticProps` 当作页面的属性，那他将**失效**。

#### 在开发环境中，会在每个请求是执行

在开发过程中 (`next dev`)，每次请求都会调用`getStaticProps`。

#### 预览模式

在某些情况下，你可能希望暂时绕过静态生成，并在**请求时渲染页面**而不是构建时。例如，你可能正在使用一个 headless CMS，并希望在草稿发布之前预览它们。

这个场景 Next.js 支持，这个特性称为**预览模式**.了解更多关于[预览模式文档](/docs/advanced-features/preview-mode.md)的信息。

## `getStaticPaths` (静态生成)

如果一个页面有动态路由（[文档](/docs/routing/dynamic-routes.md)）并且使用了 `getStaticProps`，它需要定义一个路径列表，这些路径在构建时间必须提供，用于渲染 HTML 。

如果从使用动态路由的页面导出一个名为`getStaticPaths`的 `async` 函数，则 Next.js 将静态预渲染由 `getStaticPaths` 指定的所有路径。

```jsx
export async function getStaticPaths() {
  return {
    paths: [
      { params: { ... } } // See the "paths" section below
    ],
    fallback: true or false // See the "fallback" section below
  };
}
```

#### `paths` 参数 (必须)

`paths` 属性决定了哪些路径将被预渲染。例如，假设你有一个使用名为 `pages/posts/[id].js` 的动态路由的页面。如果你从这个页面导出 `getStaticPaths` ，并返回如下所示的`paths`:

```js
return {
  paths: [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ],
  fallback: ...
}
```

然后， Next.js 会在构建时，使用`pages/posts/[id].js` 中的页面组件静态生成 `posts/1` 和 `posts/2`。

注意，每个 `params` 的值必须与页面名称中使用的参数匹配:

- 如果页面名称是 `pages/posts/[postId]/[commentId]`，则 `params` 必须包含 `postId` 和 `commentId`。
- 如果页名使用任意的路由，例如 `pages/[...slug]`，则 `params` 应包含一个数组`slug`。例如，如果这个数组是`['foo', 'bar']`，那么 Next.js 会在 `/foo/bar` 上静态生成页面。
- 如果页面使用任意的路由，支持提供`null`、 `[]`、 `undefined` 或者 `false`来渲染最根路线。例如，如果在 `pages/[[...slug]]` 指定了 `slug: false` ，则 Next.js 将静态生成页面 `/`。

#### `fallback` 参数 (必须)

由 `getStaticPaths` 返回的对象必须包含一个布尔值类型的参数， `fallback`。

#### `fallback: false`

如果`fallback` 是 `false`，那么`getStaticPaths` 未返回的任何路径都将产生一个**404页面**。如果有少量的路径可以进行预渲染，则可以这样做 - 因为它们都是在构建时静态生成的。当不经常添加新页面时，它也很有用。如果向数据源添加更多内容并需要渲染新页面，则需要再次运行构建。

下面这个示例，是预渲染一个叫做 `pages/posts/[id].js` 的博客文章。博客文章列表将通过 `getStaticPaths` 从 CMS 中获取。然后，对于每个页面，它使用 `getStaticProps` 从 CMS 中获取文章数据。这个例子也出现在[页面文档](/docs/basic-features/pages.md)中。

```jsx
// pages/posts/[id].js

function Post({ post }) {
  // Render post...
}

// This function gets called at build time
export async function getStaticPaths() {
  // Call an external API endpoint to get posts
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // We'll pre-render only these paths at build time.
  // { fallback: false } means other routes should 404.
  return { paths, fallback: false }
}

// This also gets called at build time
export async function getStaticProps({ params }) {
  // params contains the post `id`.
  // If the route is like /posts/1, then params.id is 1
  const res = await fetch(`https://.../posts/${params.id}`)
  const post = await res.json()

  // Pass post data to the page via props
  return { props: { post } }
}

export default Post
```

#### `fallback: true`

<details>
  <summary><b>示例</b></summary>
  <ul>
    <li><a href="https://static-tweet.now.sh">Static generation of a large number of pages</a></li>
  </ul>
</details>

如果 `fallback` 是 `true`，那么 `getStaticPaths` 的行为就会改变:

- 在构建时间从 `getStaticPaths` 返回的路径将被渲染到 HTML。
- 在构建时未生成的路径**不会**导致404页。相反，Next.js 将在第一个请求中提供该页面的 “fallback” 版本。（有关详细信息，请参阅下面的[“回退页面”](#fallback-pages)）
- 完成后，浏览器就会收到生成路径的 JSON。这将被用来自动渲染页面所需的属性。从用户的角度来看，页面将从后备页面更新到整个页面。
- 同时，Next.js 将此路径添加到预渲染的页面列表中。生成的页面将服务于对同一路径的后续请求，就像构建时预渲染的其他页面一样。

> 使用 [`next export`](/docs/advanced-features/static-html-export.md)时不支持 `fallback: true`。

#### Fallback 页面

页面的“fallback”版本:

- 页面的属性将是空的。
- 使用[路由](/docs/api-reference/next/router.md)，你可以检测回退是否被渲染，`router.isFallback` 将是 `true`。

下面是一个使用 `isFallback` 的例子:

```jsx
// pages/posts/[id].js
import { useRouter } from 'next/router'

function Post({ post }) {
  const router = useRouter()

  // If the page is not yet generated, this will be displayed
  // initially until getStaticProps() finishes running
  if (router.isFallback) {
    return <div>Loading...</div>
  }

  // Render post...
}

// This function gets called at build time
export async function getStaticPaths() {
  return {
    // Only `/posts/1` and `/posts/2` are generated at build time
    paths: [{ params: { id: '1' } }, { params: { id: '2' } }],
    // Enable statically generating additional pages
    // For example: `/posts/3`
    fallback: true,
  }
}

// This also gets called at build time
export async function getStaticProps({ params }) {
  // params contains the post `id`.
  // If the route is like /posts/1, then params.id is 1
  const res = await fetch(`https://.../posts/${params.id}`)
  const post = await res.json()

  // Pass post data to the page via props
  return {
    props: { post },
    // Re-generate the post at most once per second
    // if a request comes in
    revalidate: 1,
  }
}

export default Post
```

#### 当设置 `fallback: true` 真的有用吗?

如果你的应用程序有大量依赖于数据的静态页面（想想：一个非常大的电子商务站点），那么 `fallback: true` 非常有用。你希望预渲染所有产品页面，但这样你的构建将永远无法完成。

相反，你可以静态地生成一个页面子集，并对其余页面使用 `fallback: true`。当某个用户请求一个还没有生成的页面时，将看到带有加载指示器的页面。之后不久，`getStaticProps` 完成，页面将渲染请求的数据。从现在开始，每个请求相同页面的任意用户都将获得静态预渲染页面。

这确保了用户在保持快速构建和静态生成的好处的同时始终拥有快速的体验。

`fallback: true`不会_更新_生成的页面，请查看[增量静态再生](#incremental-static-regeneration)。

### 什么时候应该使用 `getStaticPaths`?

如果是使用动态路由的静态预渲染页面，则应该使用 `getStaticPaths` 。

### TypeScript: 使用 `GetStaticPaths`

在 TypeScript 中, 你可以使用从 `next` 中导出的 `GetStaticPaths` 类型:

```ts
import { GetStaticPaths } from 'next'

export const getStaticPaths: GetStaticPaths = async () => {
  // ...
}
```

### 技术技巧

#### 与 `getStaticProps` 一起使用

在具有动态路由参数的页面上使用 `getStaticProps` 时，必须使用 `getStaticPaths`。

不能将 `getStaticPaths` 和 `getServerSideProps` 一起使用。

#### 仅在服务端构建时运行

`getStaticPaths` 仅在服务端的构建时运行。

#### 只允许出现在页面组件

`getStaticPaths` 只能从**页面**导出，不能从非页面文件导出。

此外，你必须使用 `export async function getStaticPaths() {}` - 如果你把 `getStaticPaths` 当作页面的属性，那他将**失效**。

#### 在开发环境中，会在每个请求是执行

在开发过程中 (`next dev`)，每次请求都会调用 `getStaticPaths`。

## `getServerSideProps` (服务端渲染)

如果从一个页面导出一个名为 `getServerSideProps` 的 `async` 函数，那么 Next.js 将使用由 `getServerSideProps` 返回的数据在每个请求上预渲染这个页面。

```js
export async function getServerSideProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  }
}
```

`context`参数是一个包含以下键的对象:

- `params`: 包含使用动态路由的页面的路由参数。 如果页面名称为 `[id].js` ，那么 `params` 将看起来像 `{ id: ... }`。请查看[动态路由文档](/docs/routing/dynamic-routes.md)。
- `req`: [一个 HTTP IncomingMessage 对象](https://nodejs.org/api/http.html#http_class_http_incomingmessage).
- `res`: [一个 HTTP response 对象](https://nodejs.org/api/http.html#http_class_http_serverresponse).
- `query`: 查询字符串。
- `preview`: 如果页面是预览模式，则`preview`是`true`，否则为`false`。参见[预览模式文档](/docs/advanced-features/preview-mode.md)。
- `previewData`: 包含由 `setPreviewData` 设置的预览数据。参见[预览模式文档](/docs/advanced-features/preview-mode.md)。

> **注意**: 你可以在顶级作用域中导入模块，以便在 `getServerSideProps` 中使用。
> `getServerSideProps` 中使用的导入将不仅仅是客户端模块， [说明如下](#only-runs-on-server-side)。

### 简单的例子

下面是一个在请求时使用 `getServerSideProps` 获取数据并预渲染它的示例。这个例子也出现在[页面文档](/docs/basic-features/pages.md)中。

```jsx
function Page({ data }) {
  // Render data...
}

// This gets called on every request
export async function getServerSideProps() {
  // Fetch data from external API
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  // Pass data to the page via props
  return { props: { data } }
}

export default Page
```

### 什么时候应该使用 `getServerSideProps`?

只有当你需要预呈现一个必须在请求时获取数据的页面时，你才应该使用`getServerSideProps`。到第一个字节的时间(TTFB)会比 `getStaticProps` 慢，因为服务器必须计算每个请求的结果，而且如果没有额外的配置，结果不能被CDN缓存。

如果不需要预渲染数据，那么应该考虑在客户端获取数据。[点击这里了解更多](#fetching-data-on-the-client-side)。

### TypeScript: 使用 `GetServerSideProps`

在 TypeScript 中，你可以是用从 `next` 导出的 `GetServerSideProps` 类型:

```ts
import { GetServerSideProps } from 'next'

export const getServerSideProps: GetServerSideProps = async (context) => {
  // ...
}
```

如果你想得到你的属性的推断类型，你可以使用 `InferGetServerSidePropsType<typeof getServerSideProps>`，如下所示:

```tsx
import { InferGetServerSidePropsType } from 'next'

type Data = { ... }

export const getServerSideProps = async () => {
  const res = await fetch('https://.../data')
  const data: Data = await res.json()

  return {
    props: {
      data,
    },
  }
}

function Page({ data }: InferGetServerSidePropsType<typeof getServerSideProps>) {
  // will resolve posts to type Data
}

export default Page
```

### 开发技巧

#### 仅在服务端运行

`getServerSideProps` 仅在服务器端运行，从不在浏览器上运行。如果一个页面使用 `getServerSideProps`，那么:

- 当您直接请求此页面时，`getServerSideProps` 在请求时运行，此页面将使用返回的属性进行预渲染。
- 当您通过`next/link` ([documentation](/docs/api-reference/next/link.md)) 或者 `next/router` ([documentation](/docs/api-reference/next/router.md)) 在客户端做页面跳转时，Next.js 向服务器发送一个 API 请求，在服务器运行 `getServerSideProps`。它将返回包含运行 `getServerSideProps` 结果的 JSON 数据，而这些 JSON 数据将用于渲染页面。所有这些工作都将由 Next.js 自动处理，所以只要定义了 `getServerSideProps` ，就不需要做任何额外的工作。

可以使用 [这个工具](https://next-code-elimination.now.sh/)来验证 Next.js 从客户端编译包中消除了什么。

#### 只允许出现在页面组件

`getServerSideProps` 只能从**页面**导出，不能从非页面文件导出。

此外，你必须使用 `export async function getServerSideProps() {}` - 如果你把 `getServerSideProps` 当作页面的属性，那他将**失效**。

## 在客户端获取数据

如果页面包含频繁更新的数据，并且不需要预渲染数据，可以在客户端获取数据。这方面的一个例子是针对特定用户的数据。它是这样运作的:

- 首先，立即显示没有数据的页面。部分页面可以使用静态生成进行预渲染。您可以显示丢失数据的加载状态。
- 然后，在客户端获取数据并在准备好时显示它。

例如，这种方法适用于用户仪表板页面。因为仪表板是一个私有的、特定于用户的页面，所以是和 SEO 不相关的，页面不需要预渲染。数据经常更新，这需要请求时数据获取。

### SWR

Next.js 的团队创建了一个用于数据获取的 React hook，称为[**SWR**](https://swr.now.sh/)。我们强烈建议你在客户端获取数据是使用它。它处理缓存、重新验证、焦点跟踪、间隔重构等等。你可以这样使用:

```jsx
import useSWR from 'swr'

function Profile() {
  const { data, error } = useSWR('/api/user', fetch)

  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <div>hello {data.name}!</div>
}
```

[了解更多 SWR 信息](https://swr.now.sh/).

## 了解更多

我们建议你接下来阅读以下章节:

<div class="card">
  <a href="/docs/advanced-features/preview-mode.md">
    <b>预览模式:</b>
    <small>了解更多 Next.js 中有关预览模式的信息。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/routing/introduction.md">
    <b>路由:</b>
    <small>了解更多 Next.js 中有关路由的信息。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/basic-features/typescript.md#pages">
    <b>TypeScript:</b>
    <small>在你的页面中添加 TypeScript。</small>
  </a>
</div>
