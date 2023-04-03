# 什么是自我主权身份

> 原文：<https://medium.com/coinmonks/what-is-self-sovereign-identity-a8087b2bf0ea?source=collection_archive---------1----------------------->

这是我前阵子碰到的一个话题，但是没有完全理解。它没有令牌，因此不像其他区块链应用程序或基础设施组件那样受到关注。这可能也是为什么没有很多关于它是什么和它的用途的好的解释。然而，这个话题是一个基本的组成部分，并将改变我们在互联网上的互动方式。像[微软这样的公司正在这个领域投入巨资](https://identity.foundation/ion/)，所以让我们来看看这项技术背后的东西。

我们目前在网上使用身份的方式相当烦人。每个服务都需要一个唯一的用户名和密码登录，科技巨头试图用社交登录来解决这个问题，这使它稍微不那么糟糕，但并没有完全解决它。此外，他们获得了您提供这一微小改进的所有数据。

如果你想一想我们使用身份的其他领域，你会注意到在过去的几年里没有太多的变化。大多数国家仍然使用一块塑料来颁发驾照，然后持有者可以用它来证明自己的身份。但是一块塑料在网上就不太好用了。

看看申请一套新公寓，你通常需要证明你的收入、财务状况和租赁历史，这是一个繁琐的纸质过程，耗费你和房东的时间来完成。以某种方式以数字形式存在的信息不能用来证明，而是需要打印出来，并且通常由第三方验证。此外，带有你的敏感信息的打印文档可能会出现在某个几乎不知道如何使用它的人的电脑上。

自我主权认同(SSI)或许能有所帮助。

> 订阅 [**Coinmonks Youtube 频道**](https://www.youtube.com/c/coinmonks/videos) 获取每日加密新闻。

# 什么是 SSI？

自我主权身份或 SSI 是我们从物理世界中了解的身份识别过程的数字表示。比如出示我们的驾照以证明我们被允许开车，或者向房地产经纪人发送银行对账单以证明我们的收入和账户余额。这个物理过程由许多不同的部分组成，因此 SSI 也由不同的部分组成。两个最重要的构件是分散标识符(DID)和可验证证书(VC)。

**→ SSI 描述整个过程。**

作为 SSI 过程的一部分，**可验证凭证**代表物理凭证的数字版本。想想驾照。驾照是某个机构发的，用印章等花里胡哨的东西证明是他们发的。数字版本使用加密签名来证明它是由谁发布的。理解数字签名是如何工作的很重要( [Docusign 很好地解释了这个概念](https://www.docusign.com.au/how-it-works/electronic-signature/digital-signature/digital-signature-faq))。签名使得凭证可验证，即验证者可以验证凭证是否由签名者签名。在这一点上请耐心听我说，我们一会儿就进入这个过程。

![](img/6e18a8ab1c4bcf83ea4d58a7d6cc4b62.png)

**→可验证凭证是签名的数字凭证，因此是可验证的。**

**分散标识符** ( [DID](https://en.wikipedia.org/wiki/Decentralized_identifiers) )是标识符，因此可用于唯一地识别一个人或物体，而无需共享任何个人信息。它类似于使用地址生成公钥和私钥来签名和验证交易的钱包(见下面的[图片](https://freecontent.manning.com/the-basic-building-blocks-of-ssi/))。DID 通常存储在区块链这样的分散网络上，以确保信息不会被篡改。DID 用于将公钥发布到网络上，以便在分散设置中，其他人可以验证由相关私钥签名的信息。它们可以用作可验证凭证中的标识。

![](img/ef44e78198f7927c0a092e3ad409a77f.png)

**→分散标识符用于唯一识别一个人或物体，类似于加密货币钱包。**

# SSI 是如何工作的

既然我们已经完成了基础知识，让我们看看它们是如何组合在一起的。最好从一些熟悉的东西开始，比如驾照。它是由一个国家的政府颁发的，并且通常每个人都相信政府机构在许可证上的信息是正确的(即，这个人被允许驾驶)。

驾照由司机持有，可以向任何需要证明其身份和合法驾驶能力的人出示。然后，请求该证明的实体将通过检查文档并可能向发行者核实文档未被撤销来验证信息是否正确。

该图表描述了 SSI 的流程，并在下面的章节中进行了详细描述。

![](img/9d7dbe355144065179f8d2ddf865faf1.png)

# 谁颁发证书？

当以数字方式完成时，颁发证书并没有太大的不同。就驾驶执照而言，它仍然是政府的，但理论上任何人都可以颁发任何种类的证书。就像在现实世界中一样，您仍然需要信任证书的颁发者，这一点很重要，这是 SSI 不会改变或帮助的。

SSI 中重要的一点是，证书不是印在塑料卡上，而是由颁发者加密签名，这样任何人都可以验证是谁颁发了证书。这使得验证比在物理世界中容易得多。

发行者创建包含关于他自己的信息的分散标识符(DID)。DID 随后被存储在区块链上，这样每个人都可以查看这些信息并深入了解其历史。DID 还允许创建私钥和公钥。私钥签署凭证，公钥允许验证谁签署了凭证。

证书的发行者也可以通过用已被撤销的证书更新撤销寄存器来撤销证书。当验证时，验证者可以检查该寄存器以确保凭证没有被撤销。

# 你如何出示证件

一旦证书被颁发，它就属于持有者，并存放在他的钱包里。假设一家银行想要一份身份证明并请求该文档。凭证的持有者通过给予对文档的访问权而向验证者(银行)出示凭证。然后，验证者只需要检查存储在区块链上的相关信息，以查明签署文档的签名是否属于发行者，以及它是否没有被撤销。同样，验证者需要信任这里的发行者，但至少他可以确定是谁签署了凭证。

[更高级的概念使用](/mattr-global/a-solution-for-privacy-preserving-verifiable-credentials-f1650aa16093)零知识证明来隐藏实际信息，仅表示达到了某个阈值。驾照可以显示司机的年龄在 21 岁以上，但不能透露确切的年龄。

凭证可以组合成一个凭证展示，一次向验证者展示多个凭证。一个很好的例子是申请一套公寓，房东通常希望看到多份工资单和其他信息。

最好的事？你不必相信你的房东会把你的机密信息存放在一个安全的地方。他实际上不会得到一个副本来存储在他们潜在的不安全的系统上，而只是看到信息并可以验证，而不需要你给他们一个副本。

# 区块链有什么作用

这是一个开放的标准，无论有没有区块链都可以使用，但是有些好处只在使用分布式分类帐时才适用。当然也有不利的一面，每个用例都需要根据在链上存储信息是否有意义来评估。

分散标识符(did)是 JSON 文件，将它们存储在链上很有意义。它允许任何人访问信息，不可变的分类帐确保信息是防篡改的(它可以被更改，但这会创建一个新版本)。

然而，实际的证书文件并不存储在链上。大学学位的 PDF 文件太大了，无法存放在区块链上。[行星间文件系统](https://ipfs.io/#how) (IPFS)允许在对等网络中存储，将文件切割成更小的块，对内容进行哈希处理，并使用 ID 引用文件，然后可以通过唯一的链接进行请求。在这种情况下，只有链接会存储在链上。

撤销注册是另一个应该存储在链上的关键部分，这样撤销就不会被更改，并且可以在分布式分类帐的一个地方使用。

虽然 SSI 的实际实现仍然不多，但人们可以想象在以太坊或比特币上运行 SSI 的痛苦，因为 DID 的每次更改或撤销的交易成本都很高，并且交易需要时间来结算。

[Sidetree](https://identity.foundation/working-groups/sidetree.html) 试图用第二层来解决这个问题。允许将许多事务批处理成一个，并作为一个事务一次性提交到第 1 层。Sidetree 是这种工作方式的分类帐不可知规范，并且存在针对比特币(ion)和以太坊(Element)的特定实现。

# 商业应用

许多公司看到了 SSI 的潜力。[微软参与了](https://techcommunity.microsoft.com/t5/identity-standards-blog/ion-we-have-liftoff/ba-p/1441555)基于比特币的侧链实现 ion 的开发。显然，他们正在计划或者已经在他们的 Azure Active Directory 中添加了对 SSI 的支持。

IDunion 是一个由德国和欧洲公司组成的集团，旨在为分散式身份管理创建一个开放的生态系统。一些知名企业的参与将有望使 SSI 的使用更加主流化，并使最终用户能够从中受益。

![](img/11101e0bacc690e2acdee0910d784440.png)

除此之外，SSI 还可以在许多其他用例及场景中发挥作用。W3C 在他们的网站上有一个完整的图表和对每个场景的详细解释。这表明，在一个去中心化的世界里，SSI 可能成为这些非常重要的基础设施之一。

![](img/c4dc06d448951bfd6abf5621739432c1.png)

有了一大堆不同的协议和公司( [Selfkey](https://selfkey.org/) 、 [Evernym](https://www.evernym.com/) 、 [Sovrin](https://sovrin.org/) 、 [Tykn](https://tykn.tech/) 、 [Fractal](https://protocol.fractal.id/) 、…)寻址和提供 SSI 解决方案，上述场景的建立只是时间问题。

# 结束语

虽然 SSI 没有硬币，也没有我们看到的围绕 NFT 和其他硬币的大肆宣传，但它仍然是一个非常有趣和有前途的话题。想象一下这样一个未来，你拥有所有的数据，登录网站不需要任何密码，你可以快速而谨慎地与他人分享你的信息，而无需将实际数据交给他们。忘记打印我们的文件或将其发送给某个对受保护的个人数据一无所知的房地产代理机构背后漫长而令人厌倦的过程。激动人心的时刻即将到来。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 另外，阅读

*   [币安 vs FTX](https://blog.coincodecap.com/binance-vs-ftx) | [最佳(SOL)索拉纳钱包](https://blog.coincodecap.com/solana-wallets)
*   [BigONE 交易所评论](/coinmonks/bigone-exchange-review-64705d85a1d4) | [CEX。IO 审查](https://blog.coincodecap.com/cex-io-review) | [交换区审查](/coinmonks/swapzone-review-crypto-exchange-data-aggregator-e0ad78e55ed7)
*   [最佳比特币保证金交易](/coinmonks/bitcoin-margin-trading-exchange-bcbfcbf7b8e3) | [比特币保证金交易](https://blog.coincodecap.com/bityard-margin-trading)
*   [加密保证金交易交易所](/coinmonks/crypto-margin-trading-exchanges-428b1f7ad108) | [赚取比特币](/coinmonks/earn-bitcoin-6e8bd3c592d9)
*   [WazirX vs coin dcx vs bit bns](/coinmonks/wazirx-vs-coindcx-vs-bitbns-149f4f19a2f1)|[block fi vs coin loan vs Nexo](/coinmonks/blockfi-vs-coinloan-vs-nexo-cb624635230d)
*   [BlockFi 信用卡](https://blog.coincodecap.com/blockfi-credit-card) | [如何在币安购买比特币](https://blog.coincodecap.com/buy-bitcoin-binance)
*   [火币交易机器人](https://blog.coincodecap.com/huobi-trading-bot) | [如何购买 ADA](https://blog.coincodecap.com/buy-ada-cardano) | [Geco。一次回顾](https://blog.coincodecap.com/geco-one-review)
*   [加密复制交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [五大 BlockFi 替代品](https://blog.coincodecap.com/blockfi-alternatives)
*   [CoinLoan 点评](https://blog.coincodecap.com/coinloan-review)|[Crypto.com 点评](/coinmonks/crypto-com-review-f143dca1f74c) | [火币保证金交易](/coinmonks/huobi-margin-trading-b3b06cdc1519)
*   [Bybit vs 币安](https://blog.coincodecap.com/bybit-binance-moonxbt)|[stealth x 回顾](/coinmonks/stealthex-review-396c67309988) | [Probit 回顾](https://blog.coincodecap.com/probit-review)
*   [顶级付费加密货币和区块链课程](https://blog.coincodecap.com/blockchain-courses)
*   [在美国如何使用 BitMEX？](https://blog.coincodecap.com/use-bitmex-in-usa) | [BitMEX 评论](https://blog.coincodecap.com/bitmex-review)
*   [最佳期货交易信号](https://blog.coincodecap.com/futures-trading-signals) | [流动性交易所评论](https://blog.coincodecap.com/liquid-exchange-review)
*   [南非的加密交易所](https://blog.coincodecap.com/crypto-exchanges-in-south-africa) | [BitMEX 加密信号](https://blog.coincodecap.com/bitmex-crypto-signals)
*   [MoonXBT 副本交易](https://blog.coincodecap.com/moonxbt-copy-trading) | [阿联酋的加密钱包](https://blog.coincodecap.com/crypto-wallets-in-uae)
*   [Remitano 审查](https://blog.coincodecap.com/remitano-review)|[1 英寸协议指南](https://blog.coincodecap.com/1inch)
*   [MoonXBT vs Bybit vs 币安](https://blog.coincodecap.com/bybit-binance-moonxbt) | [Arbitrum:第二层解决方案](https://blog.coincodecap.com/arbitrum)
*   [买 PancakeSwap(蛋糕)](https://blog.coincodecap.com/buy-pancakeswap)|[matrix export Review](https://blog.coincodecap.com/matrixport-review)
*   [最佳免费加密信号](https://blog.coincodecap.com/free-crypto-signals) | [YoBit 评论](/coinmonks/yobit-review-175464162c62) | [Bitbns 评论](/coinmonks/bitbns-review-38256a07e161)
*   [OKEx 回顾](/coinmonks/okex-review-6b369304110f) | [Kucoin 交易机器人](/coinmonks/kucoin-trading-bot-automate-your-trades-8cf0ca2138e0) | [期货交易机器人](/coinmonks/futures-trading-bots-5a282ccee3f5)
*   [AscendEx Staking](https://blog.coincodecap.com/ascendex-staking)|[Bot Ocean Review](https://blog.coincodecap.com/bot-ocean-review)|[最佳比特币钱包](https://blog.coincodecap.com/bitcoin-wallets-india)