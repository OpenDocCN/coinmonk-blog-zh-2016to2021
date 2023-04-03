# Solidity 教程:从公共函数返回结构

> 原文：<https://medium.com/coinmonks/solidity-tutorial-returning-structs-from-public-functions-e78e48efb378?source=collection_archive---------0----------------------->

在 Solidity 的当前版本中(在撰写本文时是 0.4.13)，不可能从公共函数返回 struct。例如，如果您尝试编译以下代码，将会得到两个错误，每个尝试这样做的函数一个错误:

你可以在[浏览器-坚固度](http://ethereum.github.io/browser-solidity)中打开这个要点，链接如下:[http://ether eum . github . io/browser-坚固度/# Gist = 03 F9 e 536d 033 f 81 Fe 2 a 2d f 7 a 74 a 17 ddf&version = sol JSON-v 0 . 4 . 13+commit . fb4c B1 a . js](http://ethereum.github.io/browser-solidity/#gist=03f9e536d033f81fe2a2df7a74a17ddf&version=soljson-v0.4.13+commit.fb4cb1a.js)

正如您将看到的，编译器抱怨两个函数，`getBryn`和`getPerson`:

```
TypeError: Internal type is not allowed for public or external functions.
```

已确认，不可能返回结构。

然而，如果你已经在 Solidity 中编码一段时间了，你可能会注意到下面的工作非常好:

因为`mapping`是公共的，所以 Solidity 自动为这个函数生成一个 getter。这很奇怪，因为 getter 必须返回一个`Person`结构，对吗？让我们再深入一点。

让我们部署上面的契约并运行下面的 Javascript 代码:

好吧，这是怎么回事？

当我们要求编译器为返回一个`Person`结构的`mapping`生成一个 getter 时，编译器不应该抱怨吗？

事实证明，solc 足够聪明，可以为我们处理这个案子。注意调用`project.people('0xdeadbeef')`的返回值。它是一个数组，这就是如何将 Solidity 元组返回到 Javascript land。好了，我们知道 getter 返回的不是一个结构体，而是一个元组。**这是个大线索！**

当编译器为元素是结构的`mapping`或数组生成一个 getter 时，它会做如下事情:

如您所见，getter 只是将结构分解成一个元组。没有内部类型(如结构)被公开。结果，我们得到了我们想要的功能，没有编译错误。我们只需小心结构字段的顺序——如果顺序改变，我们必须改变与这个 getter 交互的任何代码，因为元组值的顺序也会改变。

了解了这一点，我们可以在编写自己的返回结构的函数时，从 Solidity 对这个`mapping` getter 的实现中得到启示。

# 返回结构数组

有时我们可能需要向调用者返回一个结构数组。然而，如果我们采取天真的方法，我们将再次遇到同样的限制——我们不能公开内部类型。

我们在上面了解到，我们可以“析构”一个结构，并将其作为一个元组返回。为了返回结构数组，我们将做同样的事情。返回的元组中的每个值将表示结构中的一个字段。但是因为我们试图返回许多结构(因此，每个字段都有许多值)，所以元组中的每个字段都是一个数组。

请查看以下代码，以更好地了解这可能是什么样子:

无可否认，在前端以这种格式获取数据是一件痛苦的事情。根据您的前端数据模型，您很可能需要编写函数来从接收到的数据中“重建”这些结构。我提供了一个简单的例子来告诉你如何去做:

注意，结构“析构”是(希望是)一种临时的解决方法。Solidity 的行为预计会在未来的某个时候发生变化，使这变得更加无缝。

在网络上找到我:

*   推特:[https://twitter.com/brynbellomy](https://twitter.com/brynbellomy)
*   github:【https://github.com/brynbellomy 
*   领英:【https://www.linkedin.com/in/bryn-bellomy 

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [电网交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   面向开发者的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [杠杆代币的终极指南](/coinmonks/leveraged-token-3f5257808b22)
*   [加密交易的最佳 VPNs】](https://coincodecap.com/best-vpns-for-crypto-trading)
*   [最佳加密分析或链上数据](https://coincodecap.com/blockchain-analytics) | [Bexplus 评论](https://coincodecap.com/bexplus-review)
*   [NFT 十大市场造币集锦](https://coincodecap.com/nft-marketplaces)
*   [AscendEx Staking](https://coincodecap.com/ascendex-staking)|[Bot Ocean Review](https://coincodecap.com/bot-ocean-review)|[最佳比特币钱包](https://coincodecap.com/bitcoin-wallets-india)
*   [Bitget 评论](https://coincodecap.com/bitget-review) | [双子星 vs BlockFi](https://coincodecap.com/gemini-vs-blockfi) | [OKEx 期货交易](https://coincodecap.com/okex-futures-trading)