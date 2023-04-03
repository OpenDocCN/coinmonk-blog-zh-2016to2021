# IPFS 教程:通过 NodeJS 后端发送和获取文件

> 原文：<https://medium.com/coinmonks/ipfs-tutorial-sending-and-getting-files-via-nodejs-backend-85c85ae7f6f6?source=collection_archive---------0----------------------->

大家好，我将告诉如何上传一个文件到 ipfs 网络，并通过后端从 ipfs 网络获取文件。我创建了一个路由器，用于以基本的方式获取和发送文件。为了完成本教程，我们需要 ipfs 和 ipfs-api 模块的 infura 网关。

![](img/7f15b91046ce0819daa99b3fce22370c.png)

**开始**

首先，您需要为这个项目创建一个空文件夹，以便于访问它。创建文件夹后，您需要在该文件夹中打开一个命令 shell。我假设您的计算机上安装了 npm。npm 是由 nodejs 发布的，所以如果你安装了 nodejs，你就会得到 npm。如果你没有从[这个站点](https://nodejs.org/en/)的 npm 下载节点。安装 npm 后，您需要调用。

```
npm init
```

如果要保留默认设置，包括默认名称(即文件夹名称)，可以通过单击 enter 跳过所有行。在这个过程的最后，您将得到一个 package.json 文件。

启动项目后，在文件夹中你需要创建一个 js 文件，它的名字是你想要的，这将是我们的后端

**获取模块**

在我的代码中，你可以看到有模块需求，我们只是通过 npm 下载将它们包含到项目中。他们的国家预防机制呼吁是:

```
npm install ipfs-api --savenpm install express --savenpm install fs --save
```

您需要从命令行单独调用这几行来将它们安装到项目中。当您完成安装过程时，您可以看到有一个名为 node modules 的文件夹，这是您的模块下载的文件夹。

**代码**

**准备**

在前 5 行我们定义了我们的模块。然后在第 8 行，我们在 infura 的帮助下定义了我们的 infura 网关。如果你想知道更多关于 infura 的信息，你可以看看他们的网站。他们做得很好，使以太坊和 ipfs 网络更容易连接。

为了测试的目的，我在我的后端目录中创建了一个文件来上传到 ipfs 网络。在上传文件到 ipfs 之前，由于 ipfs 的要求，我将文件更改到缓冲区。我也分享[这个链接](https://github.com/ipfs/awesome-ipfs)给你。您可以通过该链接了解有关 ipfs 的更多信息。他们创建了一个关于 IPF 的非常好的库。

当我们完成了关于 ipfs 的先决条件，剩下的代码就更容易了。我为本教程创建了添加文件和获取文件路由器，这些路由器的名称是不言自明的。

**添加文件**

在添加文件路由器中，我们使用 ipfs-api 的 add 函数和回调函数。这个函数将我们的文件添加到网络中，并返回这个文件的散列值。如果你去 gateway.ipfs.io/ipfs/RETURNED_HASH，你可以在这里看到你的文件上传。

**获取文件**

因为这个路由器的目的是学习，这个路由器不是自动的。为了获取文件，我们需要从添加文件路由器获得的散列。当我们用我们的散列值修改第 30 行时，这个路由器把你的文件记录到控制台。

感谢您阅读本教程，如果您有任何问题或建议，可以通过 [twitter](https://twitter.com/AhmetSayarli) 联系我

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](/coinmonks/top-3-telegram-channels-for-crypto-traders-in-2021-8385f4411ff4) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [购买比特币印度](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) | [Pionex 评论](/coinmonks/pionex-review-exchange-with-crypto-trading-bot-1e459d0191ea) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [印度的加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9) | [比特币储蓄账户](/coinmonks/bitcoin-savings-account-e65b13f92451)
*   [Cloudbet 赌场评论](https://coincodecap.com/cloudbet-casino-review) | [点火赌场评论](https://coincodecap.com/ignition-casino-review)
*   [币安收费](/coinmonks/binance-fees-8588ec17965) | [僵尸加密审查](/coinmonks/botcrypto-review-2021-build-your-own-trading-bot-coincodecap-6b8332d736c7) | [热点审查](/coinmonks/hotbit-review-cd5bec41dafb)
*   [MyConstant Review](https://coincodecap.com/myconstant-review) | [8 款最佳摇摆交易机器人](https://coincodecap.com/best-swing-trading-bots)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   杠杆代币的终极指南