---
description: Next.js supports API Routes, which allow you to build your API without leaving your Next.js app. Learn how it works here.
---

# API Routes

<details open>
  <summary><b>示例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes">Basic API Routes</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes-middleware">API Routes with middleware</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes-graphql">API Routes with GraphQL</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes-rest">API Routes with REST</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes-cors">API Routes with CORS</a></li>
  </ul>
</details>

API routes 为使用 Next.js 构建你自己的 **API** 提供了一种解决方案。

`pages/api` 目录下的任何文件都将作为 API 端点映射到 `/api/*`，而不是 `page`。

例如, 以下的API route `pages/api/user.js` 处理了一个 `json` 响应:

```js
export default (req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'application/json')
  res.end(JSON.stringify({ name: 'John Doe' }))
}
```

为了使 API 路由能正常工作，你需要导出(export)一个默认函数(a.k.a **请求处理器**)，并且该函数能够接收以下参数:

- `req`: 一个 [http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage) 实例, 以及一些你可以在 [这里](/docs/api-routes/api-middlewares.md) 看到的中间件
- `res`: 一个 [http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse) 实例, 以及一些你可以在 [这里](/docs/api-routes/response-helpers.md) 看到的辅助函数
要处理 API route 的不同 HTTP 方法，可以在请求处理器中使用 `req.method`，如下所示:

```js
export default (req, res) => {
  if (req.method === 'POST') {
    // Process a POST request
  } else {
    // Handle any other HTTP method
  }
}
```

请求 API，请查看本章开头列出的任何一个示例。

> 如果 API 路由[未指定CORS标头](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 则意味着它们在默认情况下 **仅是同源策略(same-origin)**。你可以通过使用[CORS中间件](/docs/api-routes/api-middlewares.md#connectexpress-middleware-support)包装出一个请求处理器来自定义此行为。

> API Routes不会增加你的客户端包大小。它们只会打包到服务端。

## 了解更多

为了获取下一步的更多信息，我们建议你接下来阅读以下章节:

<div class="card">
  <a href="/docs/api-routes/api-middlewares.md">
    <b>API 中间件:</b>
    <small>学习更多关于request的内置中间件</small>
  </a>
</div>

<div class="card">
  <a href="/docs/api-routes/response-helpers.md">
    <b>响应辅助函数:</b>
    <small>学习更多关于response的内置方法</small>
  </a>
</div>

<div class="card">
  <a href="/docs/basic-features/typescript.md#api-routes">
    <b>TypeScript:</b>
    <small>添加TypeScript到你的API Routes</small>
  </a>
</div>
