# Cadence 和 Flow 将如何革新智能合约编程

> 原文：<https://medium.com/coinmonks/how-cadence-and-flow-will-revolutionize-smart-contract-programming-607bd05b49b?source=collection_archive---------0----------------------->

通过利用资源的力量，Flow 的编程语言 Cadence 为以分类账为中心的智能合约世界带来了令人兴奋的新想法。

![](img/26a1f7b1dd4ab595f96fe6ed44beed0f.png)

Photo by [Ryan Quintal](https://unsplash.com/@ryanquintal?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

就在我写完这篇文章的时候，新闻爆出:区块链流量背后的公司 Dapper Labs 刚刚成功获得了另一轮 3.05 亿美元的融资。对我来说，这并不意外。

对，明明有 NBA 顶投。但老实说，这个项目看起来很有前景还有很多其他原因:用户友好和面向开发人员的理念，创新的扩展方法，或者只是项目背后的伟大团队，他们已经给加密世界带来了 Cryptokitties(以及 ERC721 标准)等精彩的创新。

但在我看来，真正的创新在于这项技术的核心，它的智能契约语言 **Cadence。这就是为什么我要写一系列文章来深入探索这门语言，从核心原则的概述开始。**

## 剧透:这不全是关于 NFTs 的

在深入细节之前，让我们先搞清楚一些事情。当谈到心流时，人们似乎直觉地将它与不可替代的令牌联系在一起——考虑到 NBA Top Shot 的惊人成功，这一点也不奇怪。但是如果你就此打住，你就错过了最精彩的部分。

Cadence 最具破坏性的方面之一实际上是可替换令牌的实现。因此，可替换令牌实现恰好也是 Cadence 的一个主要颠覆性范例的一个很好的说明:**面向资源**。

为了掌握这一概念，让我们先来看看一些熟悉的东西，然后逐步了解 Cadence 带来的新想法。

## 可靠性和以分类账为中心的方法

让我们从以太坊上一个可替换令牌的实现开始，一个非常简单的实现。它强调了我称之为“以分类账为中心的方法”或“集中映射”哲学的特征。

我们有一个带有两个功能的智能合约:一个造币功能，它允许我们凭空印钱；另一个是发送功能，它提供了将钱发送给其他人的可能性。但是这个契约最重要的实体是契约顶部的数据结构:**余额映射**。

```
mapping(address => uint) public balance;
```

这是本质，这是我们所需要的一切；创造价值的数据，给予以太也接受以太。这是以太坊上所有可替代代币的本质组成:一个国家。而这种状态，虽然被有些复杂的契约功能所改变和转化，却始终是万物跳动的心脏:一个中央总账。

## 中央分类账的问题

对于从事 crypto 的人来说，这很有意义，但是对于这个领域之外的人来说，可能就不是这样了。老实说，为什么要这样？

是的，我们可能会嘲笑类似“等等，我的比特币实际存储在哪里？”或者“我的钱包能装多少乙醚？”。但是就像孩子的问题一样，一旦我们笑完了，他们的答案就变得很难找到了。

让我们这样来看:在我们的日常生活中，我们将钱视为一种资源，一种我们可以触摸的东西，一种我们可以感受到的东西，一种我们可以放在枕头下的东西。果不其然，当我们把钱存入银行或在公司工作时，我们肯定会偶然发现总账，对吧。但问题是，在我们传统的货币体系中，这些分类账都参考了现实世界的资源，那些我们可以感觉或触摸或藏在钱包里的东西。

不像以太坊。在以太坊，就像在许多其他区块链网络中一样，账本实际上*就是*货币，因此在用户的不同心智模型之间产生了**不匹配。对我来说，这就是为什么密码领域的新手很难理解所有事情的主要原因。公平地说，整件事有点违背直觉，你说呢？**

此外，整个以分类账为中心的方法也会给我们带来一些技术上的麻烦。比方说，我们不仅拥有一批硬币，还拥有一些其他代币，如硬币和硬币。如果我们希望看到我们拥有的所有不同货币的所有余额，我们将不得不查看每一种货币的每一个分类账，即每一个地址的每一份智能合约，以便对我们的资金有一个总体了解。这对于开发者和用户来说都是非常乏味的，一旦你开始在不同的契约之间进行交互，事情会变得更加复杂。

现在继续这个思路到犯罪的一面。中央账本带来了巨大的安全风险。对用户来说，是价值的单一存储；对于黑客来说，这是一个蜜罐。想想那次差点杀死以太坊的 500 万美元的盗刀案。

进入:面向资源的范式。

## 面向资源的范例

Cadence 将范例从中央分类帐转移到我所说的分布式资源。在我们看实际的实现之前，我们必须谈论一下 Flow 上的账户以及它们与以太坊上的账户有何不同。有一篇关于这个主题的很棒的文章，你应该看看:

[](https://www.decentology.com/guides-and-tutorials/blockchain-accounts-flow-vs-ethereum) [## 那么，Flow 上的账户和以太坊上的账户有什么不同呢？

### 这篇博客文章是为开发者学习区块链向前发展的流程而写的，我们假设你已经有了基本的…

www.decentology.com](https://www.decentology.com/guides-and-tutorials/blockchain-accounts-flow-vs-ethereum) 

关键的要点是:与以太坊不同，Flow 没有用户和合同不同账户的概念。在 Flow 中，智能合约(以及任何种类的资源)实际上都存储在帐户本身中，一个帐户可以存储多个智能合约。它们位于帐户的一个特定子目录中，可以通过某些功能成为公共的或私有的，这个主题超出了本文的范围。

为什么这很重要？因为它允许另一种级别的数字资产所有权，即用户实际上*拥有*其帐户中的资源，而不是依赖于存储在某个远程智能合同中的外部分类账中的条目。

所以现在有趣的问题是:资源看起来怎么样？让我们从中断的地方继续，继续我们的可替换令牌示例。下面是一个可替换令牌的实现，取自流文档。

如你所见，这提出了所有权设计的一个根本不同的概念。我们没有使用用户余额来实现中央契约，而是将用户余额创建为一个单独的资源—一个保险库。并且该金库直接存储在相应用户的账户中；它*实际上*归用户所有，从而完全去中心化所有权。

为了接收令牌，用户只需将接收方接口(包含存款功能)实现到他或她的帐户中，并使其可公开调用。然后，任何其他用户都可以调用此函数，以便将令牌存放到用户的保管库中。相反，为了发送令牌，用户必须实现提供者接口(包含取款功能)，当然这一次是以私有方式。

## 真实交易:线性类型和移动操作符

retract 函数是 Cadence 真正神奇之处的体现，在我看来，它是整个实现中最有趣的部分。函数体的第一行看起来很熟悉:我们简单地从自己的令牌余额中减去令牌的数量。但是现在到了特别的部分:

```
return <- create Vault(balance: amount)
```

如我们所见，我们不只是发送代币和增加一些接收者余额。我们实际做的是创建另一个 vault 形式的资源(让我们称之为我们的子 vault ),并用我们想要发送的余额实例化这个子 vault。顺便说一句，Cadence 中的资源只能在定义它们的范围内创建，所以我们可以实例化我们的子库，但是我们不能创建没有在我们的帐户中定义的资源的实例。

这个新创建的子 vault 本身就是一个自己的资源，然后用 move 操作符(

> 当在赋值、参数或返回值中使用资源时，它将被移动到新位置，旧位置将失效。这确保了资源一次只存在于一个位置。

简而言之，move 操作符(以及线性类型的使用)防止了许多不好的事情发生，当与智能合约交互时，这些不好的事情很可能在任何其他区块链网络中发生。它有效地防止了资源的丢失或错误复制。保证资源在同一时间只能存在于一个位置。对我来说，这在数字资产所有权、可用性和安全性方面改变了游戏规则。

## 资源导向的优势

上面概述的 Cadence 面向资源的方法与中央分类帐相比有几个主要优势。

*   首先，这种方法**增强了分散化**，而分散化正是区块链技术的核心。仔细想想，Solidity 风格的可替换令牌的实现实际上是相当集中的:用一个契约来管理它们。在 Cadence 中，货币不是有一个中心故障点，而是以资源的形式分布在所有帐户中(在本例中是金库)。
*   第二，它执行**安全**。在前一点的基础上，进一步的权力下放有效地最大限度地减少了脆弱领域。我们避免建立一个吸引黑客的中央蜜罐。攻击者将不得不渗透到每个用户的每个金库，而不是抢夺一个中央合同，这有效地降低了大范围攻击的可行性。
*   第三，最终用户的**可用性**也有所提高。如果您要向其发送令牌的人没有实现该货币的接收器接口，您可以简单地不调用帐户的存款功能。这有效地结束了所有那些被错误发送并且永远无法找回的硬币。
*   最后但同样重要的是，帐户级别拥有的资源的概念更接近普通用户的心智模型。实际拥有一个存放代币的金库的想法，比必须掌握一个作为货币的分类账的想法要直观得多。这基本上就像你有一个自己的钱包，里面有整齐有序的隔层，用来存放你使用的不同硬币。

## 那么，什么更好呢？

我们正处于区块链进化的关键时刻。第一个和第二个周期已经证明，该技术有许多使用案例，甚至为大规模机构资金流入该领域打开了大门。在我看来，前面仍然有一个主要障碍:最终用户和开发人员对该技术的大规模采用，这种采用超越了投机性的参与。这是心流真正闪耀的地方。正如我们所看到的，正是底层技术的智能设计合理地支持了他们对用户友好、开发者友好的生态系统的承诺。

说了这么多，我觉得流量不会是唯一的前进方向。让我们面对现实:以太坊在可预见的未来不会有任何发展，尤其是在第二层扩展解决方案蓬勃发展的今天。它的生态系统是巨大的，许多主要的智能合同都经过了战斗测试和审计，这是一个非常重要的论点，目前还不能用于 Flow 上的任何实施。

但是当然，最大化主义者的论点几乎总是没有抓住要点，Flow 的团队(反对其他一些项目)已经非常清楚地表明，他们不想以任何方式成为“以太坊杀手”叙事的一部分。

## 多链条的未来

我相信，未来是多链的，Flow 可能会在新用户加入该领域方面迈出第一步，以及像 Blocto 这样易于使用的半托管钱包。涵盖这些区块链技术可以慢慢融入用户熟悉体验的用例是我认为 Flow 非常适合做的任务。这也与他们的整体关注点、合作伙伴的选择以及供应链上的产品发布非常一致。如果用户更具技术性，或者用例需要更复杂的工作流，总会有以太坊，支持更关键的、技术上更细粒度的实现。

无论哪种方式，Flow 都是对空间的巨大丰富，打破了智能合约编程的当前状态，并带来了全新的概念。所有这些创新的根源，本质上也是我在本文中想要展示的，在于 Cadence，这是流的智能契约语言。

这就是为什么我要对提出这种创新方法的 Flow-team 大声欢呼，并祝愿他们未来的计划一切顺利！

> 加入 Coinmonks [Telegram group](https://t.me/joinchat/EPmjKpNYwRMsBI4p) 并了解加密交易和投资

## 另外，阅读

*   最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a) | [网格交易](https://blog.coincodecap.com/grid-trading)
*   [加密复制交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [如何在 WazirX 上购买比特币](/coinmonks/buy-bitcoin-on-wazirx-2d12b7989af1)
*   [CoinLoan 点评](/coinmonks/coinloan-review-18128b9badc4)|[Crypto.com 点评](/coinmonks/crypto-com-review-f143dca1f74c) | [火币保证金交易](/coinmonks/huobi-margin-trading-b3b06cdc1519)
*   [尤霍德勒 vs 科恩洛 vs 霍德诺特](/coinmonks/youhodler-vs-coinloan-vs-hodlnaut-b1050acde55a) | [Cryptohopper vs 哈斯博特](https://blog.coincodecap.com/cryptohopper-vs-haasbot)
*   [杠杆代币](/coinmonks/leveraged-token-3f5257808b22) | [最佳密码交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [Paxful 点评](/coinmonks/paxful-review-4daf2354ab70)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南| [如何做空比特币](/coinmonks/how-to-short-bitcoin-568a2d0b4ae5)
*   [如何在印度购买比特币？](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) | [瓦济克斯评论](/coinmonks/wazirx-review-5c811b074f5b)
*   [印度比特币交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9) | [比特币储蓄账户](/coinmonks/bitcoin-savings-account-e65b13f92451)
*   [币安收费](/coinmonks/binance-fees-8588ec17965) | [僵尸加密审查](/coinmonks/botcrypto-review-2021-build-your-own-trading-bot-coincodecap-6b8332d736c7) | [热点审查](/coinmonks/hotbit-review-cd5bec41dafb)
*   [我的密码副本交易经验](/coinmonks/my-experience-with-crypto-copy-trading-d6feb2ce3ac5) | [购买硬币评论](https://blog.coincodecap.com/buycoins-review)
*   [Bybit 融资融券交易](/coinmonks/bybit-margin-trading-e5071676244e) | [币安融资融券交易](/coinmonks/binance-margin-trading-c9eb5e9d2116) | [Overbit 审核](/coinmonks/overbit-review-9446ed4f2188)
*   [加密货币储蓄账户](/coinmonks/cryptocurrency-savings-accounts-be3bc0feffbf) | [YoBit 审查](/coinmonks/yobit-review-175464162c62) | [Bitbns 审查](/coinmonks/bitbns-review-38256a07e161)
*   [Botsfolio vs nap bots vs Mudrex](/coinmonks/botsfolio-vs-napbots-vs-mudrex-c81344970c02)|[gate . io 交流回顾](/coinmonks/gate-io-exchange-review-61bf87b7078f)
*   [最佳比特币保证金交易](/coinmonks/bitcoin-margin-trading-exchange-bcbfcbf7b8e3) | [萝莉点评](/coinmonks/lolli-review-e6ddc7895ad8) | [比特币保证金交易](https://blog.coincodecap.com/bityard-margin-trading)
*   [创造并出售你的第一个 NFT](https://blog.coincodecap.com/create-nft) | [本地比特币评论](/coinmonks/localbitcoins-review-6cc001c6ed56)
*   [加密保证金交易交易所](/coinmonks/crypto-margin-trading-exchanges-428b1f7ad108) | [赚取比特币](/coinmonks/earn-bitcoin-6e8bd3c592d9) | [Mudrex 投资](https://blog.coincodecap.com/mudrex-invest-review-the-best-way-to-invest-in-crypto)
*   [如何在印度购买以太坊？](https://blog.coincodecap.com/buy-ethereum-in-india) | [如何在币安购买比特币](https://blog.coincodecap.com/buy-bitcoin-binance)
*   [顶级付费加密货币和区块链课程](https://blog.coincodecap.com/blockchain-courses) | [Pionex vs 币安](https://blog.coincodecap.com/pionex-vs-binance)
*   [MXC 交易所评论](/coinmonks/mxc-exchange-review-3af0ec1cba8c)

> [直接在您的收件箱中获得最佳软件交易](/coinmonks/newsletters/coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://medium.com/coinmonks/newsletters/coinmonks)