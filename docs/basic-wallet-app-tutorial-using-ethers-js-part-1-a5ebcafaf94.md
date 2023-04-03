# 使用 ethers.js 的基本钱包应用教程—第 1 部分

> 原文：<https://medium.com/coinmonks/basic-wallet-app-tutorial-using-ethers-js-part-1-a5ebcafaf94?source=collection_archive---------1----------------------->

![](img/d4c0e9ec944e34d2b95982e2006eb66a.png)

Photo by [Charles 🇵🇭](https://unsplash.com/photos/chB-ff0cgCU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/wallet?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

这是系列教程的第 1 部分，我们将使用 [ethers.js](https://docs.ethers.io/ethers.js/html/) 和 [hyperapp](https://github.com/jorgebucaran/hyperapp) 构建一个基本的钱包应用。我们将使用[包裹](https://parceljs.org/getting_started.html)和[浪涌](https://surge.sh/)启动应用程序。

第 1 部分— [这是我之前准备的一张](http://basic-wallet.surge.sh/)。

源代码:[https://github.com/JessTaDa/basic-wallet](https://github.com/JessTaDa/basic-wallet)

# 安装软件包

创建一个新文件夹来存放您的项目。从项目文件夹的根目录运行:

`$ npm init`

`$ npm install --save ethers`

`$ npm install --save hyperapp`

`$ npm install -g parcel`

`$ npm install -g surge`

# 创建您的基本应用程序

1.  在文本编辑器中打开项目根目录。
2.  在您的项目根目录下创建一个文件夹`src`。我们将在这里保存我们所有的前端相关文件。
3.  在`src`文件夹中，创建文件`index.js`和`index.html`。
4.  根据[包裹](https://parceljs.org/getting_started.html)设置说明，将以下代码放入您的`index.html`文件:

```
<html>
<body>
  <script src="./index.js"></script>
</body>
</html>
```

1.  将下面的代码粘贴到`index.js`中。有关更多信息，请参见[ethers . js wallet 和 signers](https://docs.ethers.io/ethers.js/html/api-wallet.html?highlight=createrandom) 文档和 [hyperapp](https://github.com/jorgebucaran/hyperapp) 示例:

```
// importing the ethers.js library
**const ethers = require('ethers');**// importing hyperapp
**import { h, app } from "hyperapp";**// Create a wallet object with privateKey and address attributes with a default state set to null.
**const state = {
  wallet: {
    privateKey: null,
    address: null
  }
}**// Create a generateWallet action in wallet, that calls the ethers.js library wallet.createRandom() method and returns a newly created privateKey and address. Set the created privateKey and address to the app state.
**const actions = {
  wallet: {
    generateWallet: () => state => {
      const wallet = ethers.Wallet.createRandom();
      return {
        privateKey: wallet.privateKey,
        address: wallet.address
      };
    },
  },
};**// Displays the wallet address and privateKey states.
// Create a button that calls the generateWallet action.
**const view = (state, actions) => (
  <div>
    <h1>Address:{state.wallet.address}</h1>
    <h1>Private Key:{state.wallet.privateKey}</h1>
    <button onclick={() => actions.wallet.generateWallet()}>
      Generate Wallet
    </button>
  </div>
);****app(state, actions, view, document.body)**
```

此外，根据 hyperapp 设置说明，记得用以下内容创建一个`.babelrc`文件:

```
{
  "plugins": [["@babel/plugin-transform-react-jsx", { "pragma": "h"  }]]
}
```

1.  在您的终端窗口中(您应该在`src`目录中)，运行`$ parcel index.html`。这会将`index.html`设置为您的 parcel 条目文件，并为您开始构建。默认情况下，它应该启动一个运行在`http://localhost:1234`上的服务器。在你的浏览器中打开一个新标签，然后访问`http://localhost:1234`，看看你的基本 hyperapp 反例是否正常工作。

# 启动您的应用

一旦您的项目准备好被部署，运行`$ parcel build index.html`。构建文件将自动创建在一个`dist`目录中。

导航到`src/dist`目录并运行`$ surge`。这将提示来自 surge.sh 的登录信息。如果需要，请创建一个新帐户并登录。一旦完成，surge 将向您提供您的应用程序已经启动的 url。点击打开 url，然后点击“生成钱包”按钮。您应该看到两个字符串，一个地址和一个私钥。

搞定了。简单的柠檬汽水！

请继续关注第 2 部分！

*特别感谢* [*尼克·多德森*](https://medium.com/u/587e336ec1ea?source=post_page-----a5ebcafaf94--------------------------------) *给予的灵感、耐心、信念、提示和希望:)*

> 另请阅读:[最佳硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)