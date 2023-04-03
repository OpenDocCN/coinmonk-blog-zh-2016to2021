# 关于比特币你必须知道的 11 件事

> 原文：<https://medium.com/coinmonks/11-things-you-must-know-about-bitcoin-60bcfa735470?source=collection_archive---------4----------------------->

## 5 分钟看懂比特币

itcoin 这个名字在 2017 年价格触及高点时变得流行起来。今天每个人都知道[比特币](https://blog.coincodecap.com/a-candid-explanation-of-bitcoin)。这也是因为它的价格波动非常大，一些国家禁止买卖它(然后它在新闻中非常多)。第一个比特币分类账开始于 2009 年 1 月 3 日(由[中本聪](https://en.wikipedia.org/wiki/Satoshi_Nakamoto)组织)。再来了解一些关于比特币的趣事。

![](img/224ca45c84f38f46450a696efb2bc8a2.png)

Photo by [Austin Distel](https://unsplash.com/@austindistel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 1.比特币是有限的:

.

*   是的，这是真的。只能开采**2100 万**个比特币。
*   截至【2020 年 12 月 8 日，几乎有**1850 万(88%)** 个比特币存在。当开采出新的区块时，这个数字将会改变。大约需要 **10 分钟**。
*   今天它的价值也是因为它像黄金或任何宝石一样是有限的。

# 2.比特币减半:

.

*   比特币减半意味着每 210000 个区块(4 年)后，采矿奖励将减半。
*   比特币推出的时候，挖矿的奖励是 50 BTC，三次减半后，现在的奖励是**6.25 BTC(2020 年 5 月以后)**。
*   比特币挖掘算法在每隔 **2016 个区块(14 天)**后改变其复杂性(工作证明哈希难题)，因此一个区块的平均时间为 **10 分钟**。
*   如果我们按天计算，那么一个区块的平均时间是 10 分钟，这意味着在一个小时内有 6 个区块(60 分钟/10 分钟)被开采，一天将有(6*24) 144 个区块。所以用 **14 天，就完成了(144*14)** **2016 块**。

> 比特币的美妙之处在于它的减半算法。

# 3.当我们提交比特币交易时发生了什么？

.

*   当我们向钱包发出发送指令时，钱包会创建一个原始交易，并通过私钥(由比特币钱包持有)对交易签名，并使用私钥进行一些数学计算，生成一个**唯一数字签名(称为交易哈希)**。
*   在第二阶段，事务散列将它存储在我们的 **mempool(存储未确认事务的内存池)**中。按照区块链协议，mempool 事务将它广播到所有节点，并验证该事务，然后将其存储在我们的 mempool 中。
*   在第三阶段，在所有节点中将会有一些挖掘节点。miner 节点从内存池中挑选事务。选取的事务将它存储在块中。所有挖掘器节点重复这个过程。现在**比赛条件**来了，无论谁赢得这场比赛，都有机会在区块链提交这个区块。
*   在第四阶段，在区块链中提交块后，**块将与每个节点**同步，您的交易将与从发送者钱包中生成的交易散列一起出现。

> 挖掘节点将优先处理费用较高的事务。

# 4.比特币交易费用谁拿？

.

*   无论哪个挖掘节点将在竞争条件中获胜，都将允许提交块。该节点将获得该块中存在的所有交易费用。

# 5.比特币使用 POW(工作证明)共识。

.

*   共识是一种策略，其中一组计算机用于维护具有相同状态的相同分类帐。
*   有许多一致的算法是存在的。比特币协议遵循 POW(工作证明)共识。在战俘，有一个数学难题给所有的矿工提交区块。所有的矿工使用计算能力来解决难题(**竞赛条件**)。

> **比特币协议在每 2016 个区块(2 周)中自我调整谜题复杂度，以将平均区块时间维持在 10 分钟。**

# 5.比特币叉子是什么意思？

.

*   加密货币世界中的分叉含义是用新规则或相同规则来拆分链。在分叉链中，数据库将与我们分叉时相同。这意味着如果某个地址在分叉前有 10 个硬币，那么在分叉后它也有 10 个新硬币，并且运行链也有 10 个硬币。
*   两条路分叉了。**硬叉**和**软叉**。
*   **硬叉**意为用新的规则和条例来分割链条。
*   **软叉**意为用同样的规则分割链条。

# 6.我们可以用更高的费用重新提交未确认的交易吗？

.

*   是的，我们可以。我们必须启用 **bitcoin.conf** 文件中的 **RBF (Replace By Fee)** 选项。
*   要重新提交交易，首先使用 **bitcoin-cli** 命令设置 **minfee** ，然后使用交易费用参数执行 **bumpfee** 命令。

> 你只需要交易散列。

# 7.比特币中的地址类型有哪些？

.

*   比特币有三种地址。 **Legacy、SegWit(隔离证人**)和 **Segwit2x。**
*   **遗留地址**是最初的比特币地址。所有的钱包都支持发送和接收比特币。
*   **SegWit 地址**是软叉。这个地址将证据数据存储在一个单独的有效负载中，因此与 Legacy 相比要轻得多。
*   **Segwit2x 地址**扩展 Segwit 地址的大小为 **2MB** 。大多数未成年人都不支持。

# 8.什么是 BIP 协议？

.

*   BIP(比特币改进提案)是一个维护比特币社区变革的标准。2011 年 8 月 19 日，Amir Taaki 创建了第一个 BIP 协议。

**受欢迎的 BIP 协议有:**

*   **BIP32** :分层确定性钱包。
*   **BIP39** :生成确定性密钥的助记码。
*   **BIP44** :确定性钱包的多账户层级。

# 9.可以设置比特币节点吗？

.

*   **可以，每个人都可以设置**一个比特币节点。如果我们同步整个比特币块，那么我们需要超过 350GB 的空间。别担心。你不需要这么大的容量。有一个**修剪**选项可用。您可以将此选项设置到 **bitcoin.conf** 文件中，就像这样`prune=2000`表示比特币区块链只维护**最后 2000 块**。

> 如果我们用 prune 运行比特币节点，会有一些限制。

*   如果你有好奇心**用源代码**设置一个比特币节点。跟着这篇[文章](/coinmonks/how-to-setup-a-bitcoin-node-using-source-code-linux-2e9d18fc26c1)。

# 10.私钥是什么？

.

*   私钥就像一个**密码**。如果有人有你的私钥，就可以控制你的比特币。
*   私钥与你的比特币地址有关，它是一个 **256 位**的数字( **64 个字符**)。
*   比特币钱包会保管好你的私人钥匙。您可以使用 **bitcoin-cli** 命令`**dumpprivkey**` **导出私钥。**

> 不要和任何人分享你的私人密钥。

# 11.理解关键词:Merkle Root、CoinBase、SHA256？

.

*   **Merkle Root** :是存储一个块的所有事务信息的 hash。这意味着每个块在块头中都有一个 Merkle 根，这是块的一个简短表示。
*   **CoinBase** :该术语用于**阻止奖励+费用**转移给矿工，因此该交易由 CoinBase 发布，或者我们可以说它是一个 coinbase 交易。
*   **SHA256** :这是一种由**比特币力量**使用的哈希技术，可以将任何数据转换成哈希格式。它只以一种方式工作，这意味着我们可以将数据转换为哈希，但我们不能反向操作(哈希到数据)。

[](https://guptaarnish-it.medium.com/what-is-blockchain-have-you-ever-heard-of-the-blockchain-b70eaa9c04c8) [## 什么是区块链？你听说过区块链吗？

### 区块链是许多常见问题的答案。

guptaarnish-it.medium.com](https://guptaarnish-it.medium.com/what-is-blockchain-have-you-ever-heard-of-the-blockchain-b70eaa9c04c8) 

过去四年来我一直在区块链的 T4 工作，我对它有很深的了解。在这里，我告诉你我与区块链有关的想法，这样我的努力将对你们所有人有用。

今天就在这里把这些都说完吧。希望能提振你对比特币的信心。

***感谢阅读。我希望你能通过区块链找到你的目的地。***

> **也可以分享给身边的人，让他们记住你。**

## 另外，阅读

*   最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [Uniswap API](https://bitquery.io/blog/uniswap-pool-api) —如何获取 Uniswap 数据？
*   [Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   FTX 密码交易所评论
*   [Bybit 交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   [bits gap vs 3 commas vs quad ency](https://blog.coincodecap.com/bitsgap-3commas-quadency)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [莱杰纳米 S vs 特雷佐 one vs 特雷佐 T vs 莱杰纳米 X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   Bitsgap 评论——一个轻松赚钱的加密交易机器人
*   [Quadency Review](/coinmonks/quadency-review-a-crypto-trading-automation-platform-3068eaa374e1) -为专业人士打造的加密交易机器人
*   [PrimeXBT 评论](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   [alt ready 审查](https://blog.coincodecap.com/altrady-reivew)
*   [埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)
*   [SecuX Stone 评论](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)
*   [BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |赚取高达 8.6%的加密利息
*   [开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)

> [直接在您的收件箱中获得最佳软件交易](/coinmonks/newsletters/coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://medium.com/coinmonks/newsletters/coinmonks)