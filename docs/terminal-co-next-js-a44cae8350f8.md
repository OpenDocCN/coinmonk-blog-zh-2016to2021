# Terminal.co+next . js

> 原文：<https://medium.com/coinmonks/terminal-co-next-js-a44cae8350f8?source=collection_archive---------0----------------------->

## 使用 Terminal.co 和 IPFS 部署 Nextjs 应用程序

![](img/19ae885df5edea6720b5c3beb7b232f8.png)

# 概观

我们将使用创建一个 Next.js 应用程序，并用[Terminal.co](http://Terminal.co)部署它。整个过程需要 10 分钟。

工具:

*   Terminal.co 账户
*   GitHub 帐户
*   node.js/npm

# 步骤 1:在 Github 上建立一个回购协议

创建一个空存储库并克隆它。

![](img/f47905c886818b37bd81401c97559982.png)

使用以下内容创建 Next.js 应用程序:

```
$ mkdir nextjs && cd nextjs
$ npm init --y
$ npm install next react react-dom
```

![](img/55d64640404a3cdcc343f88113afd9bb.png)

打开`package.json`并添加以下脚本

```
"scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start",
    "export": "next export"  
}
```

![](img/2ee0907b53c94d69201d6d377826a32f.png)

在根目录下创建一个`next.config.js`文件

```
module.exports = {
  exportTrailingSlash: true,
  exportPathMap: function() {
    return {
      '/': { page: '/' }
    };
  }
};
```

![](img/90c4877f7654748db51b12fd8029612c.png)

让我们创建一些页面:

创建一个名为`pages`的文件夹

内页，创建`index.js`

```
// index.js
import Link from "next/link";export default function Index() {
  return (
    <div>
      <h1> Index </h1>
      <Link href="/about">
        <a> About </a>
      </Link>
    </div>
  );
}
```

还有`about.js`

```
// about.js
export default function About() {
  return (
    <div>
      <h1> About </h1>
    </div>
  );
}
```

它应该看起来像这样

![](img/70df17f70d70591fc8a2e295b8dfba8e.png)

要进行测试，运行`npm run dev`并访问 localhost:3000

![](img/d44d2207143d966589a09cf3dfa670dc.png)

git 添加、提交、推送

![](img/21b538ff5eef4b519962aff7bcf9c254.png)

[https://github.com/Terminal-Systems-Example/nextjs](https://github.com/Terminal-Systems-Example/nextjs)

# 步骤 2:设置 Terminal.co

签到:[https://sites.terminal.co/](https://sites.terminal.co/)

使用 Github 登录

![](img/2b0d95ceb3856851650a535f716db013.png)

添加新网站

![](img/ea96ac534a800f752d858c7dacd4e222.png)

用 Github 连接。

![](img/84aa0ee700d072796a3a87d5f6003be0.png)

选择 Next.js 存储库。

![](img/894fa1904592cef6d52545f8432774bf.png)

要创建新站点:

构建命令:`npm install && npm run build && npm run export`

发布目录:`out`

部署网站

![](img/43a3b5c1af3c24e78c319e35eb8ebd15.png)

完成后，查看您的网站。

![](img/f56acc260eab4da2fbd12a3ca4c2677b.png)

您可以使用提供的域名查看网站。

`https://<your-custom-domain>.tmnl.co`

或者向 CID 核实。

`https://ipfs.io/ipfs/<CID>`

![](img/8d70e61d14279c663f790b80fcfe3e97.png)

# 第三步:更新

每当您对 GitHub 进行更改时,“终端”会自动重新部署您的网站。确保提供的域名将保持不变，并将指向新的 CID。这使你能够在 IPFS 上建立快速的现代网站。

[![](img/e9dbce386c4f90837b5db529a4c87766.png)](https://coincodecap.com)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)