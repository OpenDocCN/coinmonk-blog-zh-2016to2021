# 符号与泰索斯(第一部分)

> 原文：<https://medium.com/coinmonks/symbol-vs-tezos-part-1-483f966ea436?source=collection_archive---------8----------------------->

## 共识协议:收获和烘烤

![](img/36d385202a1e2535cefa36a2fed5a0c0.png)

薯条对英国人来说是薯条，对美国人来说是薯条；他们是一样的，略有不同。象征丰收和 Tezos 烘焙；他们都使用了扭曲的利害关系证明协议。

## 符号的位置+

Symbol 是第二代 NIS1 (NEM 基础设施系统 1)。自 NIS1 以来，它一直在使用股权证明，只是做了一些改动。重要性证明(PoI)用于 NIS1。帐户生成阻止的概率基于其重要性分数，该分数考虑了帐户的既得利益和帐户的活跃性。

符号使用的协议称为 PoS+。它保留了对重要性分数的参考，并且也运行节点的帐户将获得更好的分数。PoS+中也引入了终结性。

是的，重要性分数是基于帐户的，而不是基于节点的。这是因为一个账户不一定要运行一个节点才能收获。它可以将其重要性分数委托给运行节点的另一个帐户，而不会损害其股份的安全性。

在 NIS1 和 Symbol 中创建新块的过程称为收获。我之前在“以太坊 2.0 vs Symbol(第二部分):共识协议”中讨论过他们的协议

[](/coinmonks/ethereum-2-0-vs-symbol-part-2-consensus-protocols-73e0a2589242) [## 以太坊 2.0 vs 符号(第二部分):共识协议

### NEM 从风险证明(PoS)衍生产品开始，以太坊一直计划从工作证明(PoW)转移到…

medium.com](/coinmonks/ethereum-2-0-vs-symbol-part-2-consensus-protocols-73e0a2589242) 

> 加入社区[https://discord.com/invite/xymcity](https://discord.com/invite/xymcity)建设 xymcity，就像你为模拟城市所做的那样。

## 泰佐斯的 LPoS

Tezos 的共识协议是流动性利害关系证明(LPoS)。代表是维护网络的节点，他们扮演 3 个角色，面包师、支持者和原告。面包师提出一个阻碍，一个背书人认可它，咄，如果发现有恶意行为，原告报告。

这是一个更具包容性的协议，允许用户将其股份委托给代理人，以便参与区块的创建，即使他们没有足够的 tez(Tezos 的本地货币)作为代理人。在写这篇文章的时候，要成为一名代表，你需要 8，000 tez (1 卷),并且有一个计划将这个数字降低到 2，000 tez。委托他们的股份的用户称为委托人。

> 请参考 [Tezos 区块链浏览器](https://tzkt.io/)了解最新区块时间、周期长度以及每个新区块生成的新 tez 等。更新后，以下信息可能会有变化。

每一次掷骰子都有优先权去造一个方块。在[大约 30 秒](https://tzkt.io/)内创建一个块(计划在下次更新时减少到 15 秒)。如果被给予优先权的面包师的掷骰子未能产生阻挡，机会将传递给下一个。创建块后，优先级列表将会更新。然后，新的区块将被认可。虽然每个区块只有一个面包师，但会有多个背书者，其中一个背书者可能有一个以上的卷。

> 如果你还没有看够《绝命毒师》，可以在 [Baking Bad](https://twitter.com/TezosBakingBad) 加入 Tezos 社区，或者在 [better-call-dev](https://better-call.dev/) 查看它的智能合同浏览器。

## Symbol 和 Tezos 协议的主要区别

1.  **方块创造奖励**:每创造一个方块，符号方块收获者将获得交易费和新的 XYM 奖励。为每个新创建的块预设新 XYM 的数量。Tezos block baker 将获得运营(又名交易)费和[网络奖励](https://opentezos.com/baking/reward#inflation)。
2.  **奖励冻结期**:收割者将立即收到他们的奖励(以最终结果为准)，一个固定的百分比将自动分享给它将其重要性分数委托给的节点。对于泰佐斯来说，奖励只有在 5 个周期后才会解冻。
3.  **委托**:运行一个节点不需要矿车的符号。它可以将其重要性分数委托给运行节点的账户[。收获者将与它委派的节点分享它的奖励。对 Tezos 来说，情况正好相反。一个面包师至少要有一个面包卷。如果一个帐户没有足够的 tez 来获得滚动资格，它可以将其股份委托给另一个代理人。代表将与委托人分享奖励。](/coinmonks/ethereum-2-0-vs-symbol-part-4-nodes-9448bca93a7e?sk=5153cd46eb83a98bb014a739f8959d21)
4.  **冻结赌注**:没有 XYM 会被冻结以获得成为符号收割者的资格。但是，如果帐户余额下降到 10，000 XYM 以下，它将不再有资格收获。对于 Tezos，[奖励将被冻结 40960 块(5 个循环)](https://opentezos.com/baking/reward#delegating-reward)。这不适用于授权人。
5.  **验证者或认可奖励**:符号中的验证者投票决定一个区块的终结。它需要大量股份才有资格成为验证者。在 Tezos 中，被选为签署人的代表将签署提议的区块。
6.  **处罚**:目前为止符号没有处罚。对于 Tezos 来说，如果原告发现面包师或代言人恶意行为，他们的股份将被没收，一半的金额将奖励给原告。
7.  **终结**:符号采用确定性终结，而 Tezos 的是概率性终结。

如果我想参与搭建积木，我会有什么选择？

如果我有一些 tez，但没有足够的资源来运行一个节点，那么 Tezos 将是我的选择。虽然我的技术援助中心将帮助面包师选择面包卷，但对我来说重要的是[选择一个好的面包师](https://baking-bad.org/docs/where-to-stake-tezos/)来授权。

如果不介意运行一个节点，但没有足够的 XYM 来收获，符号将是我的选择。我将从我的节点上的矿车分享的奖励中获得。收获者的重要性分数越高，我获得的机会也就越大。

在这两种情况下，运行一个节点会给你更多的控制，特别是在选择委派给你的帐户/节点的帐户时。

感谢 [Tezos Telegram 社区](https://t.me/baking_bad_chat)回答我的一些问题。

*参考文献:*

1.  [https://www . smart link . so/te zos-vs-cardano-the-ultimate-comparison/](https://www.smartlink.so/tezos-vs-cardano-the-ultimate-comparison/)
2.  [https://tzstats.com/](https://tzstats.com/)
3.  [https://tzstats.com/docs/guide](https://tzstats.com/docs/guide)
4.  [https://opentezos.com/](https://opentezos.com/tezos-basics/liquid-proof-of-stake/)
5.  https://blog . nomadic-labs . com/faster-finality-with-Emmy . html
6.  [https://docs.symbolplatform.com/index.html](https://docs.symbolplatform.com/index.html)
7.  [https://tezos.gitlab.io/alpha/consensus.html#emmy](https://tezos.gitlab.io/alpha/consensus.html#emmy)
8.  [https://cryptoeconomicsystems . pub pub . org/pub/neuder-自私-行为-tezos/release/10](https://cryptoeconomicsystems.pubpub.org/pub/neuder-selfish-behavior-tezos/release/10)

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

blog.coincodecap.com](https://blog.coincodecap.com/best-hardware-wallet-bitcoin)