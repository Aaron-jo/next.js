---
description: Next.js 页面是在 pages 目录下的文件导出的 React 组件。在这里学习他们是怎样工作的。
---

# Pages

> 本文档适用于 Next.js 9.3 或更高版本。如果你使用的是较旧版本的 Next.js，请参阅[先前版本的文档](https://nextjs.org/docs/tag/v9.2.2/basic-features/pages)。

在 Next.js 中，页面是从 `pages` 目录中的 `.js`、`.jsx`、`.ts` 或者 `.tsx` 文件导出的 [React 组件](https://reactjs.org/docs/components-and-props.html)。每个页面都使用其文件名作为路由。

**例如**: 如果你创建了一个命名为 `pages/about.js` 的文件并导出一个如下所示的 React 组件，则可以通过 `/about` 路径进行访问。

```jsx
function About() {
  return <div>About</div>
}

export default About
```

### 具有动态路由的页面

Next.js 支持具有动态路由的页面。例如，如果你创建了一个命名为 `pages/posts/[id].js` 的文件，那么就可以通过 `posts/1`、`posts/2` 等类似的路径进行访问。

> 要了解有关动态路由的更多信息，请查看[动态路由文档](/docs/routing/dynamic-routes.md)。

## 预渲染

默认情况下，Next.js 将**预渲染**每个页面。这意味着 Next.js 会预先为每个页面生成 HTML 文件，而不是由客户端 JavaScript 来完成。预渲染可以带来更好的性能和 SEO 效果。

每个生成的 HTML 文件都与该页面所需的最少 JavaScript 代码相关联。当浏览器加载一个页面时，其 JavaScript 代码将运行并使页面完全具有交互性。(此过程称为 _hydration_。)

### 两种形式的预渲染

Next.js 具有两种形式的预渲染:**静态生成（Static Generation）** 和**服务器端渲染（Server-side Rendering）**。这两种方式的不同之处在于为页面生成 HTML 页面的**时机**。

- [**静态生成 (推荐)**](#static-generation-recommended): HTML 在**构建时**生成，并在每次页面请求时重用。
- [**Server-side Rendering**](#server-side-rendering): 在**每次页面请求时**重新生成 HTML。

重要的是，Next.js 允许你为每个页面**选择**预渲染的方式。你可以创建一个 “混合渲染” 的 Next.js 应用程序：对大多数页面使用“静态生成”，同时对其它页面使用“服务器端渲染”。

出于性能考虑，相对服务器端渲染，我们更**推荐**使用**静态生成**。 CDN 可以在没有额外配置的情况下缓存静态生成的页面以提高性能。但是，在某些情况下，服务器端渲染可能是唯一的选择。

你还可以将**客户端渲染**与静态生成或服务器端渲染一起使用。这意味着页面的某些部分可以完全由客户端 JavaScript 呈现。要了解更多信息，请查看[数据获取](/docs/basic-features/data-fetching.md#fetching-data-on-the-client-side)的文档。

## 静态生成 (推荐)

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

如果一个页面使用了**静态生成**，在**构建时**将生成此页面对应的 HTML 文件。这意味着在生产环境中，运行 `next build` 时将生成该页面对应的 HTML 文件。然后，此 HTML 文件将在每个页面请求时被重用，还可以被 CDN 缓存。

在 Next.js 中，你可以静态生成**带有或不带有数据**的页面。接下来我们分别看看这两种情况。

### 生成不带数据的静态页面

默认情况下，Next.js 使用 “静态生成” 来预渲染页面但不涉及获取数据。如下例所示:

```jsx
function About() {
  return <div>About</div>
}

export default About
```

请注意，此页面在预渲染时不需要获取任何外部数据。在这种情况下，Next.js 只需在构建时为每个页面生成一个 HTML 文件即可。

### 有数据的静态生成

某些页面需要获取外部数据以进行预渲染。有两种情况，一种或两种都可能适用。在每种情况下，你都可以使用 Next.js 提供的特殊功能:

1. 你的页面**内容**取决于外部数据: 使用 `getStaticProps`.
2. 你的页面**路径**取决于外部数据: 使用 `getStaticPaths` (通常还要同时使用 `getStaticProps`).

#### 场景 1: 页面**内容**取决于外部数据

**例如**: 你的博客页面可能需要从 CMS（内容管理系统）中获取博客文章列表。

```jsx
// TODO: Need to fetch `posts` (by calling some API endpoint)
//       before this page can be pre-rendered.
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

export default Blog
```

要在预渲染时获取此数据，Next.js 允许你从同一文件 `export` 一个名为 `getStaticProps` 的 `async` 函数。该函数在构建时被调用，并允许你在预渲染时将获取的数据作为 `props` 参数传递给页面。

```jsx
function Blog({ posts }) {
  // Render posts...
}

// This function gets called at build time
export async function getStaticProps() {
  // Call an external API endpoint to get posts
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

要了解有关 `getStaticProps` 工作原理的更多信息，请查看[数据获取](/docs/basic-features/data-fetching.md#getstaticprops-static-generation)的文档。

#### 场景 2: 页面路径取决于外部数据

Next.js 允许你创建具有**动态路由**的页面。例如，你可以创建一个名为 `pages/posts/[id].js` 的文件用以展示以 `id` 标识的单篇博客文章。当你访问 `posts/1` 路径时将展示 `id: 1` 的博客文章。

> 要了解有关动态路由的更多信息，请查看[动态路由的文档](/docs/routing/dynamic-routes.md)。

但是，在构建 `id` 所对应的内容时可能需要从外部获取数据。

**例如**: 假设你只向数据库添加了一篇博客文章（关于 `id: 1`）。这种情况下，你只想在构建时针对 `posts/1` 进行预渲染。

稍后，你又添加了第二篇文章，关于 `id: 2`。这时，你希望对 `posts/2` 也进行预渲染。

因此，预渲染的页面**路径**取决于外部数据。为了解决这个问题，Next.js 允许你从动态页面（在这里是 `pages/posts/[id].js`）中 `export` 一个名为 `getStaticPaths` 的 `async` 函数。该函数在构建时被调用，并允许你指定要预渲染的路径。

```jsx
// This function gets called at build time
export async function getStaticPaths() {
  // Call an external API endpoint to get posts
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => `/posts/${post.id}`)

  // We'll pre-render only these paths at build time.
  // { fallback: false } means other routes should 404.
  return { paths, fallback: false }
}
```

同样在 `pages/posts/[id].js` 中，你还需要导出 `getStaticProps` 以便可以获取 `id` 所对应的博客文章的数据并进行预渲染:

```jsx
function Post({ post }) {
  // Render post...
}

export async function getStaticPaths() {
  // ...
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

要了解有关 `getStaticPaths` 工作原理的更多信息，请查看[数据获取的文档](/docs/basic-features/data-fetching.md#getstaticpaths-static-generation)。

### 什么时候应该使用静态生成?

我们建议使用**静态生成**(带数据和不带数据)，因为你的页面可以构建一次，由 CDN 提供服务，这使得它比让服务器在每个请求上呈现页面要快得多。

你可以对许多类型的页面使用静态生成，包括：

- 地推页面
- 博客文章
- 电子商贸产品目录
- 协助和文件

你应该问问自己：“我能在用户请求这个页面之前预渲染吗？”如果答案是肯定的，那么你应该选择静态生成。

另一方面，如果你无法在用户请求之前预渲染页面，那么静态生成就**不是**一个好主意。也许你的页面显示频繁更新的数据，页面内容在每个请求上都会改变。

在这种情况下，你可以执行以下任一操作:

- 将静态生成与**客户端渲染**一起使用：你可以跳过页面某些部分的预渲染，然后使用客户端 JavaScript 来填充它们。要了解有关此方法的更多信息，请查看[数据获取的文档](/docs/basic-features/data-fetching.md#fetching-data-on-the-client-side)。
- 使用**服务器端渲染:** Next.js 针对每个页面的请求进行预渲染。由于 CDN 无法缓存该页面，因此速度会较慢，但是预渲染的页面将始终是最新的。我们将在下面讨论这种方法。

## 服务端渲染

> 也被称为 "SSR" 或 “动态渲染”。

如果页面使用的是**服务器端渲染**，则会在**每次页面请求时**重新生成页面的 HTML。

要对页面使用服务器端渲染，你需要 `export` 一个名为 `getServerSideProps` 的 `async` 函数。服务器将在每次页面请求时调用此函数。

例如，假设你的某个页面需要预渲染频繁更新的数据（从外部 API 获取）。你就可以编写 `getServerSideProps` 获取该数据并将其传递给 `Page` ，如下所示:

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

如你所见，`getServerSideProps` 类似于 `getStaticProps`，但两者的区别在于 `getServerSideProps` 在每次页面请求时都会运行，而在构建时不运行。

要了解有关 `getServerSideProps` 的工作原理的更多信息，请查看我们的[数据获取文档](/docs/basic-features/data-fetching.md#getserversideprops-server-side-rendering)

## 摘要

我们已经讨论了 Next.js 的两种预渲染形式。

- **静态生成 (推荐):** HTML 是在**构建时**`getStaticProps` 函数（如果需要还可以导出 `getStaticPaths` 函数）。对于可以在用户请求之前预先渲染的页面来说，这非常有用。你也可以将其与客户端渲染一起使用以便引入其他数据。
- **服务端渲染:** HTML 是在**每个页面请求**时生成的。要设置某个页面使用服务器端渲染，请导出 `getServerSideProps` 函数。由于服务器端渲染会导致性能比“静态生成”慢，因此仅在绝对必要时才使用此功能。

## 了解更多

我们建议你接下来阅读以下章节:

<div class="card">
  <a href="/docs/basic-features/data-fetching.md">
    <b>数据获取:</b>
    <small>了解更多 Next.js 中关于数据获取的信息。</small>
  </a>
</div>

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
