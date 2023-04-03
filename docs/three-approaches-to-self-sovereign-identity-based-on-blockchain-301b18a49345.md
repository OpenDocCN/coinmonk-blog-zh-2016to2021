# 基于区块链的自我主权认同的三种路径

> 原文：<https://medium.com/coinmonks/three-approaches-to-self-sovereign-identity-based-on-blockchain-301b18a49345?source=collection_archive---------5----------------------->

## 在这篇短文中，我们研究了 SSI 概念如何映射到区块链基础设施的三种不同方法。

![](img/6a7bebb4335583e0a9db78d101fc33fb.png)

Teatro Augusteo (Salerno, Italy), detail of the roof (photo by Rosario De Chiara)

这是关于 SSI，自我主权身份的系列文章的第一篇

在这篇短文中，我们简要地比较了实现基于公共账本/区块链的自我主权身份(SSI)系统的三种不同方法。我们打算通过考虑区块链上存储了多少信息来对这些方法进行分类。一般来说，区块链在 SSI 生态系统中的作用是通过保存颁发和请求的证书和表示的散列来跟踪参与者之间的交互。

*   第一种方法由 [Hyperledger Indy](https://www.hyperledger.org/use/hyperledger-indy) 使用:区块链 Indy 基于 is [全会](https://github.com/hyperledger/indy-plenum)，这是一个特定领域的区块链，负责收集发行者/验证者/持有者之间发生的交互。SSI 是区块链的唯一目的，而凭据、身份和演示则是“一等公民”。
*   第二种方法是像 [Alastria](https://alastria.io/) 这样的基于 smartcontracts 的解决方案所采用的方法:身份、凭证和演示被保存在通用以太坊兼容区块链上的智能合同存储器内的专门注册表中。凭证散列、演示、DID 文档片段、凭证模式保存在 smartcontracts 中托管的专门注册中心，这些注册中心负责保护对给定参与者的访问。
*   第三种方法在某种程度上源自前一种方法，并且是由 [uPort/Serto](https://www.uport.me/) 追求的方法:区块链有一个单一的注册中心，只跟踪凭证的撤销，其他的一切，凭证发布，表示请求/响应的生命周期，凭证模式，可信的发布者/验证者，在规范之外，每个参与者可以向任何人发布它喜欢的任何凭证；任何进一步的要求，对谁可以做什么，必须从头开始设计和开发的额外执行。

Indy、Alastria 和 uPort/Serto 这三种方法可以放在不同的类别中进行比较。

> 订阅 [**Coinmonks Youtube 频道**](https://www.youtube.com/c/coinmonks/videos) 获取每日加密新闻。

# 分散标识符(did)创建

这三种方法之间的差异在创建新的指定经营实体的过程中是显而易见的，一般来说，每个系统中的指定经营实体对应的是什么。

在 Indy 中，可以为演员之间的每个交互生成 DID，每个 DID 是控制器的同义词；这是消除单一身份(如公民)与其证件/证明之间任何关联机会的极值比率。这非常有效地避免了拥有一个 DID 列表的风险，例如，拥有一个退休的驾驶执照。

Alastria 通过在创建 DID 时部署一个名为身份代理的智能合同副本来处理 DID；智能合同的地址成为标识字符串的一部分。身份代理 smartcontract 用于屏蔽交易，以修改注册管理机构的状态，规避将不同交易关联到单个 DID 的风险。

uPort/Serto 采用了最轻量级的方法:每个 Ethereum 地址都是一个 DID，但这不容易关联，因为没有注册中心跟踪证书颁发或演示响应。在撤销注册表中只跟踪被撤销的证书的散列，并且它不包含对该证书被颁发给的 DID 的任何引用。

# 讨论

Indy 有一种清晰的方法来保护每个参与者的隐私，方法是投入大量精力来处理同义词的生成。另一方面，Alastria 为定制提供了很多表面，比如实现复杂的策略来处理授权:您可以让注册中心收集某些特定类型的发行者，例如，您可以允许每个发行者可以处理的凭证模式上的策略，等等。uPort/Serto 可以被视为介于这两种方法之间，它在注册中心(撤销列表)中具有严格的必要性，除此之外，它并没有关闭让采用者实现更具表达性的东西的大门。

在背景中，Indy 需要一个非常特殊的、单一用途的区块链(Plenum)安装，而 Alastria 和 uPort/Serto 可以很容易地安装在任何以太坊区块链上，无论是公共安装还是私人安装，这使得 SSI 只是通用区块链上的一个用例。

*感谢* [*弗朗西斯科·祖罗洛*](https://www.linkedin.com/in/francesco-zurolo-6481002/) *和* [*保罗·赛维洛*](https://www.linkedin.com/in/paolo-servillo-86b4a926/) *对本文的有趣反馈*

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 另外，阅读

*   [最佳期货交易信号](https://blog.coincodecap.com/futures-trading-signals) | [流动性交易回顾](https://blog.coincodecap.com/liquid-exchange-review)
*   [3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92) | [Pionex 评论](https://blog.coincodecap.com/pionex-review-exchange-with-crypto-trading-bot) | [Coinrule 评论](/coinmonks/coinrule-review-2021-a-beginner-friendly-crypto-trading-bot-daf0504848ba)
*   [莱杰 vs n rave](/coinmonks/ledger-vs-ngrave-zero-7e40f0c1d694)|[莱杰 nano s vs x](/coinmonks/ledger-nano-s-vs-x-battery-hardware-price-storage-59a6663fe3b0) | [币安评论](/coinmonks/binance-review-ee10d3bf3b6e)
*   [Bybit 交易所评论](/coinmonks/bybit-exchange-review-dbd570019b71) | [Bityard 评论](https://blog.coincodecap.com/bityard-reivew) | [CoinSpot 评论](https://blog.coincodecap.com/coinspot-review)
*   [3 commas vs crypto hopper](/coinmonks/3commas-vs-pionex-vs-cryptohopper-best-crypto-bot-6a98d2baa203)|[赚取秘密利息](/coinmonks/earn-crypto-interest-b10b810fdda3)
*   最好的比特币[硬件钱包](/coinmonks/hardware-wallets-dfa1211730c6) | [BitBox02 回顾](/coinmonks/bitbox02-review-your-swiss-bitcoin-hardware-wallet-c36c88fff29)
*   [BlockFi vs 摄氏度](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630) | [Hodlnaut 审核](/coinmonks/hodlnaut-review-best-way-to-hodl-is-to-earn-interest-on-your-bitcoin-6658a8c19edf) | [KuCoin 审核](https://blog.coincodecap.com/kucoin-review)
*   [Bitsgap 评审](/coinmonks/bitsgap-review-a-crypto-trading-bot-that-makes-easy-money-a5d88a336df2) | [Quadency 评审](/coinmonks/quadency-review-a-crypto-trading-automation-platform-3068eaa374e1) | [Bitbns 评审](/coinmonks/bitbns-review-38256a07e161)
*   [加密复制交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [Coinmama 审核](/coinmonks/coinmama-review-ace5641bde6e)
*   [印度的加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9) | [比特币储蓄账户](/coinmonks/bitcoin-savings-account-e65b13f92451)
*   [OKEx vs KuCoin](https://blog.coincodecap.com/okex-kucoin) | [摄氏替代品](https://blog.coincodecap.com/celsius-alternatives) | [如何购买 VeChain](https://blog.coincodecap.com/buy-vechain)
*   [币安期货交易](https://blog.coincodecap.com/binance-futures-trading)|[3 comas vs Mudrex vs eToro](https://blog.coincodecap.com/mudrex-3commas-etoro)
*   [如何购买 Monero](https://blog.coincodecap.com/buy-monero) | [IDEX 评论](https://blog.coincodecap.com/idex-review) | [BitKan 交易机器人](https://blog.coincodecap.com/bitkan-trading-bot)
*   [CoinDCX 评论](/coinmonks/coindcx-review-8444db3621a2) | [加密保证金交易交易所](https://blog.coincodecap.com/crypto-margin-trading-exchanges)
*   [Bookmap 评论](https://blog.coincodecap.com/bookmap-review-2021-best-trading-software) | [美国 5 大最佳加密交易所](https://blog.coincodecap.com/crypto-exchange-usa)
*   [如何在 FTX 交易所交易期货](https://blog.coincodecap.com/ftx-futures-trading) | [OKEx vs 币安](https://blog.coincodecap.com/okex-vs-binance)
*   [CoinLoan 评论](https://blog.coincodecap.com/coinloan-review) | [YouHodler 评论](/coinmonks/youhodler-4-easy-ways-to-make-money-98969b9689f2) | [BlockFi 评论](https://blog.coincodecap.com/blockfi-review)