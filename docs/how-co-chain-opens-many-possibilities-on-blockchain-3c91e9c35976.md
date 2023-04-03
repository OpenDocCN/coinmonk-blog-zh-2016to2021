# Co-chain 如何在区块链上开启多种可能性？

> 原文：<https://medium.com/coinmonks/how-co-chain-opens-many-possibilities-on-blockchain-3c91e9c35976?source=collection_archive---------2----------------------->

## 一些混合区块链允许在其主链上创建许多副链，在这篇文章中，你将了解创建和运行副链的最佳区块链平台。

![](img/7e081f95e682b1f556b3f70a7743dbb0.png)

Image credit:[unplash.com](https://unsplash.com/) and edited at:[canva.com](https://www.canva.com/)

大多数智能区块链项目都带有主链和多层协议，这意味着如果你必须实施智能合约、计算和 dApps，那么你可以在区块链层实施所有这些东西，但你不能改变主链的性质，这意味着如果主链是公共区块链，那么它仍然是公共区块链，你不能根据你的要求改变区块链的性质。

因此，您需要选择一个支持副链的区块链项目，这意味着您可以与主链同时运行自己的链，副链可能由主链驱动，但仍受其所有者的控制。在本文中，我们将讨论一些支持区块链项目的副链，并尝试找出运行您的副链的最佳区块链。

# 嘉手纳:

在比特币中，存在单个主链，当一个新的块被挖掘出来时，它会将其哈希 id 发送到前一个块，通过这种方式，每个块都相互连接，因此一旦一个块被挖掘出来并放入分类帐中，它就不能被修改。在这里，Kadena 是比特币协议的可扩展版本，这意味着这里不是一个主链，而是有一个以上的链，每个链并行传播块，当一个新的块被挖掘时，它不仅将其哈希 id 发送到其前一个块，还将其哈希 id 发送到每个并行挖掘的共链块{ [**参考**](https://siliconangle.com/2020/01/15/kadena-completes-launch-scalable-public-blockchain-full-smart-contract-support/) }。

Kadena 声称，通过扩展比特币协议，它成为了一个安全的区块链，像以太坊一样，它支持智能合约实施和 oracle 支持，以改善其在现实世界中的用例。

但关键问题是它使用工作证明挖掘，我们都知道[挖掘证明有很多缺点](https://www.geeksforgeeks.org/proof-of-work-pow-consensus/)并且不能每秒处理大量交易，但由于 Kadena 是比特币的可扩展版本，所以当 Kadena 中的链数量增加时，每秒的交易也会增加，但我们知道采矿界正在向利益证明挖掘发展，因此 ethererum 2.0 即将到来{ **参考-** [**1**](https://www.forbes.com/sites/oluwaseunadeyanju/2020/01/23/jp-morgan-spinoff-kadena-is-bringing-scalability-to-pow-blockchain/#eded5a96ec97) **，** [**2 因此，出现的关键问题是，在没有矿商的情况下，嘉手纳将如何扩大交易规模？还有为什么矿工会开采嘉手纳？如果和比特币一样，那么它的挖矿阻力肯定会随着时间增加{**](https://www.coindesk.com/everything-you-need-to-know-about-ethereum-2-0) **[**参考**](https://www.nature.com/articles/d41586-018-07283-3) }。**

# 波尔卡多:

这是一个创新的区块链，允许你借助 [substrate](https://www.substrate.io/) 框架在波尔卡多特区块链上创建自己的区块链，你的区块链可以与波尔卡多特区块链通信，波尔卡多特声称从第一天起就提供安全{ [**参考**](https://www.forbes.com/sites/laurashin/2019/05/14/how-polkadot-hopes-to-help-blockchains-scale/#7d6ec29a13b3) }。

中继链是波尔卡多特区块链的核心，我们可以在主链上构建两种类型的链，即副链和副线程，其中副链是长期链，而副线程是临时链，采用现收现付模式{ [**参考**](https://polkadot.network/parathreads-parathreads-pay-as-you-go-parachains/) }。

要运行 parachain，您必须通过治理提案或 parachain slot 拍卖来保留一个 slot，对于 parathreads，有一个固定的注册费用，该费用实际上会比获得 parachain slot 的成本低得多。

插槽预订过程通过拍卖进行，这是一个昂贵而复杂的过程，但简而言之，我可以说你需要购买并持有 DOTs token 才能赢得拍卖{ [**参考**](/polkadot-network/polkadot-parachain-slots-f3f051d41699) }。

# 阿尔格兰德:

Algorand 是由图灵奖得主麻省理工学院教授希尔维奥·米卡利创建的无许可下一代区块链。阿尔格兰德·区块链让所有的组织、个人和政府都能够在那里创建和运营自己的、独立的区块链，就像阿尔格兰德·区块链上的一条连锁链。Algorand 授权他们不仅可以控制区块链的性质(可以是私有或公共的共链)，还可以选择他们自己的验证器，让您能够控制您的共链交易{Reference}。

要经营你的合作链，你不需要通过任何拍卖，也不需要持有一定数量的阿尔戈斯硬币来创建或经营你的合作链，所以它证明了阿尔戈兰特是一个真正的无许可区块链。

您将在 Algorand 区块链上创建或运行的辅链可以轻松地与主链和其他辅链进行通信{ [**参考**](https://algorandcom.cdn.prismic.io/algorandcom/e9797285-04fd-4460-9a75-dba7e6f9c313_Co-Chain_04022020.pdf) }。

# 结论:

在本文的研究之后，我发现一些像 Kadena 这样的项目正在创建 co-chain 来改进他们自己的服务，像 Polkadot 和 Algorand 这样的项目为他们的用户提供了 co-chain 设施。

我相信 Polkadot 是一个安全、快速、可扩展的区块链，但它的目标是让企业和商业在 Polkadot 区块链上运行自己的区块链，因为持有 parachain 插槽的成本对每个人来说都不便宜。但是，阿尔格兰德是一个快速、安全、可扩展、分散、廉价的连锁企业，它的目标是企业、商业、政府和个人，所以每个人都可以体验区块链技术的力量。

> **作者详情:**
> 
> **姓名-吉坦德拉·奈克**
> 
> **电子邮件-Jitencrackit2@gmail.com**

## 另外，阅读

*   最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   [莱杰 vs 特雷佐](/coinmonks/ledger-vs-trezor-best-hardware-wallet-to-secure-cryptocurrency-22c7a3fd391e)
*   了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)
*   [3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92)
*   [AAX 交易所评论](/coinmonks/aax-exchange-review-2021-67c5ea09330c) |推荐代码、交易费用、利弊
*   [Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   [FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   [n 零审核](/coinmonks/ngrave-zero-review-c465cf8307fc)
*   [比特交换评论](/coinmonks/bybit-exchange-review-dbd570019b71)
*   [3Commas vs Cryptohopper](/coinmonks/cryptohopper-vs-3commas-vs-shrimpy-a2c16095b8fe)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   最佳 [monero 钱包](https://blog.coincodecap.com/best-monero-wallets)
*   [莱杰纳米 s vs x](https://blog.coincodecap.com/ledger-nano-s-vs-x)
*   [bits gap vs 3 commas vs quad ency](https://blog.coincodecap.com/bitsgap-3commas-quadency)
*   [莱杰纳米 S vs 特雷佐 one vs 特雷佐 T vs 莱杰纳米 X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   Bitsgap 评论——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   [埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)
*   [SecuX Stone 点评](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)
*   [BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |从您的密码中赚取高达 8.6%的利息