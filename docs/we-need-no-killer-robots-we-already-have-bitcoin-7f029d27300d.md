# 我们不需要杀手机器人，我们已经有比特币了

> 原文：<https://medium.com/coinmonks/we-need-no-killer-robots-we-already-have-bitcoin-7f029d27300d?source=collection_archive---------7----------------------->

## 解释比特币是如何完美地设计成让人类走上自我毁灭之路的

多年来，人们一直担心失控的技术发展可能导致凶残的人工智能机器人一心想推翻他们的人类霸主。其中一些恐惧被电影和书籍中的流行文化所渲染，但它们足够深刻，足以引发抗议技术发展的反技术运动。

![](img/5b3af87f2e7c944f07e6ff11e4df0f34.png)

Photo by [Xu Haiwei](https://unsplash.com/@mrsunburnt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/ai?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

然而，当比特币在 2008 年首次出现并在十年内席卷全球时，我们谁也没有意识到这一威胁已经成为现实，尽管形式大不相同。相反，我们许多人张开双臂欢迎它。

在某些方面，比特币是一个远比典型的拟人化钢铁对手更优越、更邪恶的敌人。它无处不在地存在于我们的数字空间中，没有有形的形式可供我们分解，并且完全有能力不费吹灰之力(字面上)就消灭人类。

正如我们将要看到的，这种危险的意图深深地埋藏在它无害的机制中。

> 订阅 [**Coinmonks Youtube 频道**](https://www.youtube.com/c/coinmonks/videos) 获取每日加密新闻。

# 工程奇迹

审视比特币及其底层的区块链技术，人们不禁对这种解决[拜占庭将军问题](https://river.com/learn/what-is-the-byzantine-generals-problem/#:~:text=The%20Byzantine%20Generals%20Problem%20is,agree%20on%20a%20certain%20truth%3F)的优雅而创新的解决方案的构想表示敬畏。

作为一个去中心化的货币交易系统，比特币并不是一个单一的创新突破。相反，它是跨越密码学、数据加密、金融、经济学、博弈论甚至会计学的折衷思想的综合。

可以说，真正的突破在于 Satoshi 如何将一切整合在一起，以实现这个安全但不可信的网络系统。它是如此紧密地交织在一起，以至于如果你去掉任何一部分，这个系统就会不可挽回地受到损害，甚至无法运行。这里没有浪费。

冒着重复在互联网上找到的相同解释的风险，我旨在提供一个关于比特币的关键组成部分、它们的目的以及它们如何作为一个整体巧妙地联系在一起的简单而简明的纲要。

## 区块链账本

通俗地说，区块链是一个加密的数字分类账数据库，包含所有过去交易的记录。然而，区块链系统的一个定义性特征是分类账是分散的，数据被分解并存储在一个长长的区块链中。每十分钟，一个小的事务池(1Mb)被组合成一个块，并一次一个地附加到区块链中(通常是这样)。

从技术上来说，有了区块链本身，或者任何其他可靠的会计分类账系统，我们已经克服了*‘重复支出’*问题——没有人能比他们拥有的花费更多，因为我们有交易记录来支持这种说法。就这么简单。

但是，如果区块链就这样放任不管，会有明显的安全隐患。

**问题(1):** 恶意用户可以通过恶意接管服务器或拒绝服务攻击(DDoS)来篡改或关闭总账系统。

**问题(2):** 在一个分权的区块链，没有监督的中央权力机构。我们赋予谁(或什么)权力和责任来决定数百万笔交易的正确顺序，努力维护分类账记录——整个系统的支柱——并迫使所有其他参与者遵守分类账历史的一个特定版本？

这是其他概念发挥作用的地方。

## 通过权力下放实现安全

没有一个集中的实体来管理区块链网络也有它的好处。分散化要求必须公开分类账数据，这实质上使每个完整节点都成为区块链的服务器；它保存分类账的副本，可以验证交易，还可以交叉检查其他节点进行的交易。

为了接管区块链，大多数节点需要处于攻击主机的控制之下，这是一种极不可能的情况。

## 达成共识

然而，拥有一个分散的网络会带来建立共识的问题。为了使公共账本可靠，我们需要让所有参与者都同意它的单一版本。

乍一看，这似乎是一个很难解决的问题，但事实上，区块链总账是由一连串的区块串起来的，这实际上大大简化了问题。

通过归纳证明，如果参与者可以对要添加的特定区块达成共识，那么我们可以推断出，我们可以对所有后续区块达成共识，从而对区块链达成共识。

因此，这里真正的问题是——我们如何让所有参与者同意将谁的区块添加到区块链？这里，Satoshi 聪明地实现了另一种机制来解决这个难题——工作证明。

## 工作证明

工作证明是一个计算难题，必须由矿工解决，然后他们才能创建一个有效的采矿区块。在整个求解过程中，他们花费计算精力来猜测正确的密钥(或“nonce”)，该密钥将生成可以求解该块的散列。

让竞争对手来解决这个问题有双重作用:

## (1)这是一个执行十分钟规则的机制

工作证明和哈希函数作为杠杆，允许比特币调整挖掘难度。如果混合中有更多的矿工(因此有更多的计算能力)，代码会自动向上调整难度，这样平均仍然需要 10 分钟才能解决。

## (2)是比特币共识机制的选项选择

如果说账本系统是比特币的脊梁，共识机制就是它的心脏。这是数字版的赛跑，旨在通过公开比赛选出一名获胜者。同样重要但微妙的一点是，结果确保所有参与者一致向赢家让步。

因此，游戏规则必须对每个人都公平、透明和一致。必须最大限度地减少有争议的灰色区域。

在比特币中，规则非常简单。最快解开石块的矿工赢得比赛，赢得将其石块附加到区块链上的特权。

## 奖金是多少？

虽然比特币是以数字形式存在的，但挖掘所涉及的成本是非常真实的。在 2008 年，你也许可以用你的个人电脑做到，但是现在，[你将需要积累 9 年的家庭用电来解决一个街区](https://www.nytimes.com/interactive/2021/09/03/climate/bitcoin-carbon-footprint-electricity.html?)。矿工们必须为这些巨额电费得到补偿。

这就是货币激励发挥作用的地方。成功添加确认区块的矿工将获得 BTC 形式的区块奖励和交易费。以目前每 BTC 4.2 万美元的价格和 6.25 新铸造硬币的现行汇率作为整体奖励，这当然不是一个非常糟糕的交易。

作为交换，这些矿工通过积极参与采矿竞争来支持网络基础设施的维护和安全。

# 那么问题出在哪里？

了解了比特币的运作方式后，现在是我们欣赏人类心理——我们与生俱来的贪婪——在比特币手中发挥如此之好的独创性的好时机。

推动比特币被采用的叙事基调一直是乌托邦式的和渴望的，欢呼一个未来，来自每个国家的个人可以在单一数字货币下团结起来，而没有一个容易腐败和易犯错误的中央权力机构。公民成为货币的使用者和保管者，预示着经济模式和货币体系的新时代。

然而，在这种光明和崇高的外表下隐藏着一种更邪恶的动力——比特币的整个系统都是基于培养、放大和利用人类之间的竞争来达到自己的目的。这与最初呼吁团结和自由的号角相去甚远。

## 比特币滋生竞争……

关于它的架构和设计，有一些非常相关的问题需要问，特别是在激励分配方面。当我们研究比特币的机制时，我们可以观察到一种对超竞争环境的故意倾斜。

比特币的支付结构是一个高风险高回报的系统。每 10 分钟，就有 100 多万名矿工用他们的计算能力相互竞争以赢得区块。尽管事实上成群结队的矿工为了找到正确的散列密钥已经用尽了惊人的力量，但只有唯一的赢家得到了块奖励。

幸运的赢家获得一大笔奖金，而其他人在新一轮重新开始之前一无所有。诱人但高风险的支付结构及其彩票式的游戏化吸引了更多矿商，加剧了该领域的竞争。

随着比特币价格飙升至 6.5 万美元的高点，采矿已经成为一项利润丰厚的业务。考虑到这项业务的巨大潜在优势，矿商们已经开始投资于更专业的设备，如 ASICs，以便在竞争中占据优势。这导致了一场不断升级的集中计算能力的军备竞赛，使得矿工们可以更快地解决难题。

术语“更快”在这里是相对于的*，在这个词的字面意义上是痛苦的。无论一个人能积累多少计算火力，都不可能在不到十分钟的时间内挖掘出一个一致的区块。这只会确保你有更高的概率超越你的竞争对手——暂时的。*

哈希难度的自动调节在一个地方设置了一个人为的障碍，没有人可以跨越——这场比赛由无数令人满意的检查站组成，但也有一条无限期推迟的终点线。这是一场难以想象的令人筋疲力尽的比赛，要求每一个赛跑者始终保持领先，只有当每一个赛跑者都昏倒在跑道上时才结束。

## …因为比特币需要竞争

这就引出了一个问题——比特币能被重新设计以获得竞争力优势吗？

是的，但也不是。

是的，因为从技术上讲，比特币可以自由调整其激励结构。理论上可以实现一种更公平、更包容的支付方式，即向更多矿工支付奖金。[一些人甚至提出了对所有参与者进行平均分配的想法，作为维护网络的补偿，这大大降低了采矿业的竞争性。](https://scholarworks.sjsu.edu/cgi/viewcontent.cgi?article=1432&context=etd_projects)

但这些变化不利于比特币的基础。比特币的去中心化本质必然意味着需要有公开的压力来防止节点间的勾结。确保这一点的唯一方法是激励参与的节点无情地相互竞争。

当我们允许节点彼此合作时，这违背了比特币的精神，并最终破坏了它的安全性。只有通过积极的挖掘行为，网络难度才会增加，并要求工作证明。这一要求提高了*对网络的信任度*,因为劫持网络或协调 51%的攻击成本极高。潜意识里，采矿的高成本也增加了 BTC 的感知价值，推高了其市场价值。

在这一点上，我们需要想一想，人类正在被比特币愚蠢地玩弄于股掌之间吗？我们是否在不知不觉中设计了一个完美的系统，它表面上拥有自己的生命，并奴役我们无休止地为它工作？

## 代价是什么？

比特币的支持者可能会叫屈，激烈地声称比特币给数百万人带来了数不清的财富，比特币和其他加密货币价格的爆炸性增长就证明了这一点。我们在这里不是要争论这些说法的真实性，而是要质疑这些奇迹般的数字财富源泉的来源。

事实是，我们所说的这些财富，从我们的土地和自然资源中被毫无必要地*解锁了*——借用企业界的行话。

虽然比特币只存在于数字世界，但它对我们生活的物理环境有着非常真实和可怕的影响。由于其对共识机制工作证明的依赖，比特币已经成为一个巨大的能源黑洞，年电力消耗率超过了*整个国家。*这就是比特币的真实成本——以牺牲我们的生物圈为代价，对能源和电力的贪得无厌。

一些人认为，这些消费模式很难减少全球范围内的能源使用总量。然而，如前所述，比特币有一种邪恶的能力，可以让我们陷入能源支出不断上升的恶性循环。如果我们不做出有意识的努力，打破这种恶性循环，结束军备竞赛，能源使用将呈指数增长。

对于一个已经处于生存环境危机毁灭边缘的世界来说，数百万人仍在兴高采烈地砍伐我们的自然资源来生成数字代币，这一事实看起来是如此的超现实和荒谬——我们甚至还没有触及秘密市场是如何在我们的金融系统中释放资本主义剥削的。

如果我们不清醒过来，比特币已经准备好带头破坏我们的自然栖息地。人类将永远生活在技术先进文明的耻辱中，这个文明给自己带来了厄运。如果在幸运的情况下，我们避免了这场危机，比特币必须成为一个警示，永远不要设计出如此善于利用人性弱点的技术。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 另外，阅读

*   [什么是保证金交易](https://blog.coincodecap.com/margin-trading) | [美元成本平均法](https://blog.coincodecap.com/dca)
*   [支持卡审核](https://blog.coincodecap.com/uphold-card-review) | [信任钱包 vs 元掩码](https://blog.coincodecap.com/trust-wallet-vs-metamask)
*   [Exness 回顾](https://blog.coincodecap.com/exness-review)|[moon xbt Vs bit get Vs Bingbon](https://blog.coincodecap.com/bingbon-vs-bitget-vs-moonxbt)
*   [如何开始通过加密贷款赚取被动收入](https://blog.coincodecap.com/passive-income-crypto-lending)
*   [用信用卡购买密码的 10 个最佳地点](https://blog.coincodecap.com/buy-crypto-with-credit-card)
*   [加拿大最佳加密交易机器人](https://blog.coincodecap.com/5-best-crypto-trading-bots-in-canada) | [Bybit vs 币安](https://blog.coincodecap.com/bybit-binance-moonxbt)
*   [用于 Huobi 的加密交易信号](https://blog.coincodecap.com/huobi-crypto-trading-signals) | [Swapzone 审查](/coinmonks/swapzone-review-crypto-exchange-data-aggregator-e0ad78e55ed7)
*   最佳[密码交易机器人](https://blog.coincodecap.com/best-crypto-trading-bots) | [购买索拉纳](https://blog.coincodecap.com/buy-solana) | [矩阵导出评论](https://blog.coincodecap.com/matrixport-review)
*   [Coldcard 评论](https://blog.coincodecap.com/coldcard-review) | [BOXtradEX 评论](https://blog.coincodecap.com/boxtradex-review)|[uni swap 指南](https://blog.coincodecap.com/uniswap)
*   [阿联酋 5 大最佳加密交易所](https://blog.coincodecap.com/best-crypto-exchanges-in-uae) | [SimpleSwap 评论](https://blog.coincodecap.com/simpleswap-review)
*   [购买 Dogecoin 的 7 种最佳方式](https://blog.coincodecap.com/ways-to-buy-dogecoin) | [ZebPay 评论](https://blog.coincodecap.com/zebpay-review)
*   [最佳期货交易信号](https://blog.coincodecap.com/futures-trading-signals) | [流动性交易所评论](https://blog.coincodecap.com/liquid-exchange-review)
*   [3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92) | [Pionex 评论](https://blog.coincodecap.com/pionex-review-exchange-with-crypto-trading-bot) | [Coinrule 评论](/coinmonks/coinrule-review-2021-a-beginner-friendly-crypto-trading-bot-daf0504848ba)
*   [莱杰 vs n rave](/coinmonks/ledger-vs-ngrave-zero-7e40f0c1d694)|[莱杰 nano s vs x](/coinmonks/ledger-nano-s-vs-x-battery-hardware-price-storage-59a6663fe3b0) | [币安评论](/coinmonks/binance-review-ee10d3bf3b6e)
*   [Bybit Exchange 审查](/coinmonks/bybit-exchange-review-dbd570019b71) | [Bityard 审查](https://blog.coincodecap.com/bityard-reivew) | [CoinSpot 审查](https://blog.coincodecap.com/coinspot-review)