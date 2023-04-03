# 拯救 NFT

> 原文：<https://medium.com/coinmonks/rescuing-an-nft-fd0acccfa25a?source=collection_archive---------6----------------------->

![](img/319a4e57b86f638a9d96365384004903.png)

Story of a rescue mission. Photo by [Egor Myznik](https://unsplash.com/@vonshnauzer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/rescue?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

正如你们中的一些人所知，我在业余时间作为一名自由职业者从事以太坊开发/咨询工作。我已经存在了足够长的时间(快 4 年了)，我已经在加密货币领域看到了许多奇怪的东西。但是嘿，这也是我喜欢它的原因之一！

于是，一个用户看到了我在以太坊 StackExchange 上关于如何使用[MEV](https://cryptobriefing.com/what-is-mev-ethereums-invisible-tax-explained/)(Miner/最大提取值)的相当含糊的[回答](https://ethereum.stackexchange.com/a/112099/31933)，私下找我谈了这件事。他也有类似的问题。

## **问题**

用户(好吧，从现在开始:客户端)收到了一个 NFT 作为对一些工作的报酬。这位客户对密码不是很了解，他接受了钱包私钥形式的 NFT:NFT 在钱包里，他得到了钱包的私钥。

现在，这种方法有一个主要问题:无论是谁给了客户端这个私钥，他也知道这个私钥。加密领域的规则编号 uno:**永远不要和任何人分享你的私人密钥**。不幸的是，这种方法还有另一个问题:一些盗版机器人知道私钥。

![](img/43f1fd603390bb081bcf5376a3a19622.png)

Dangerous pirate stealing our Eth. Photo by [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/pirate?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

那么，如果机器人知道私钥，他们为什么不干脆清空钱包呢？这是一个非常有效的问题，我也没有答案——我的最佳猜测是，机器人只是不够聪明，没有意识到钱包里有一个有价值的 NFT。此外，我不知道为什么或如何机器人得到了私钥。

机器人*足够聪明的是，每当钱包收到乙醚时，它们就会偷走它。因此，不可能通过常规方式将 NFT 转移出去——每当客户试图发送 Eth 来支付煤气费时，机器人就会偷走它。*

## **Flashbots 来救援**

所以如果我们不能派人去营救 NFT，我们能做什么呢？要是我们能把两种交易捆绑在一个原子束里就好了:给钱包提供乙醚，把 NFT 转移出去。不幸的是，基本的以太坊交易系统不支持这样的功能。

我来救援了！

MEV 允许一个人私下贿赂区块链的矿工，将多个交易捆绑在一起(除了其他事情之外)，在一个单一的原子束中:要么所有的交易成功，要么没有。捆绑包的事务也被承诺包含在相邻的块中，因此没有机器人可以在事务之间窃取 Eth。

然而，利用 MEV 并不简单。幸运的是，有一个很棒的项目叫做 [Flashbots](https://docs.flashbots.net/) ，它有帮助你利用 MEV 的工具。

![](img/68e9a9fb4d0eca10bdb2b2b60ecb73a8.png)

Flashbots are awesome

## 首次尝试使用 Flashbots

Flashbots 项目启发了一名社区成员创建了一个以用户友好的方式追回资产的网站。不幸的是，该网站有两个问题:

1.  它不支持 NFTs (ERC-721)
2.  它使用一种过时的交易奖励模式(前 [EIP-1559](https://notes.ethereum.org/@vbuterin/eip-1559-faq)

我的第一次尝试是开始修改网站来做我们需要的事情，但结果是这个项目比我最初预期的要大一点。经过一两个小时的快速编码，我放弃了这种方法。

那时，客户已经找到了一篇关于一个非常相似问题的好的[博客文章](https://steviep.xyz/txt/compromised)。几周前我读过《邮报》,但已经完全忘记了。这篇文章有一个链接指向使用的的[源代码，看起来编辑起来容易多了。此外，客户有一个懂技术的朋友，他们可以在他们结束时使用脚本，所以不需要花哨的网站。我叉开存储库，再次开始工作。](https://github.com/scyclow/flashbot)

## 我们得救了，万岁

经过一些小的反复试验，这个脚本成功了。万岁！

![](img/222ff42b2d5a0fa53a48523890873bdf.png)

Celebratory cookies for everyone. Photo by [Vyshnavi Bisani](https://unsplash.com/@vyshnavibisani?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/cookies?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

如您所愿，可以随意使用[库](https://github.com/microbecode/flashbot)用于您自己的目的。我试着添加一些评论，让它更容易理解。

我希望这篇文章对你有所帮助。如果您有任何问题，欢迎致电 [http://t](http://t) 与我们联系。我/Lauri_P

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 也阅读

[](https://blog.coincodecap.com/crypto-exchange) [## 最佳加密交易所| 2021 年十大加密货币交易所

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/crypto-exchange) [](https://blog.coincodecap.com/crypto-lending) [## 2021 年 10 大最佳加密贷款平台| CoinCodeCap

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/crypto-lending) [](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [## 2021 年最佳免费加密交易机器人

### 2021 年币安、比特币基地、库币和其他密码交易所的最佳密码交易机器人。四进制，位间隙…

medium.com](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) [## 最佳 4 个加密交易信号电报通道

### 这是乏味的找到正确的加密交易信号提供商。因此，在本文中，我们将讨论最好的…

medium.com](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) [](https://blog.coincodecap.com/blockfi-review) [## BlockFi 评论 2021:利弊和利率| CoinCodeCap

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/blockfi-review) [](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) [## 如何在印度购买比特币？2021 年购买比特币的 7 款最佳应用[手机版]

### 如何使用移动应用程序购买比特币印度

medium.com](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) [](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b) [## 加密税务软件——五大最佳比特币税务计算器[2021]

### 不管你是刚接触加密还是已经在这个领域呆了一段时间，你都需要交税。

medium.com](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b) [](https://blog.coincodecap.com/best-hardware-wallet-bitcoin) [## 存储比特币的最佳加密硬件钱包[2021] | CoinCodeCap

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/best-hardware-wallet-bitcoin) [](/coinmonks/pionex-review-exchange-with-crypto-trading-bot-1e459d0191ea) [## Pionex 评论 2021 |免费加密交易机器人和交换

### Pionex 是为交易自动化提供工具的后起之秀。Pionex 上提供了 9 个加密交易机器人…

medium.com](/coinmonks/pionex-review-exchange-with-crypto-trading-bot-1e459d0191ea)