---
description: Next.js 支持为 public 目录下存放的静态文件（例如图片）提供对外访问服务。在此文档中你将了解到这一功能是如何工作的。
---

# 静态文件服务

Next.js 支持将静态文件（例如图片）存放到根目录下的 `public` `public` 目录下存放的静态文件的对外访问路径以 (`/`) 作为起始路径。

例如，如果你添加了一张图片到 `public/my-image.png` 路径，则以下代码就能访问到此图片:

```jsx
function MyImage() {
  return <img src="/my-image.png" alt="my image" />
}

export default MyImage
```

此文件夹还可用于存放 `robots.txt` 文件、Google 站点验证文件以及任何其它静态文件（包括 .html 文件）！

> **注意**: 请勿为 public 改名。不能更改名称，它是唯一用于服务静态资产的目录。

> **注意**: 请确保静态文件中没有与 `pages/` 目录下的文件重名的，否则这将导致错误。
>
> 更多信息: <http://err.sh/next.js/conflicting-public-file-page>
