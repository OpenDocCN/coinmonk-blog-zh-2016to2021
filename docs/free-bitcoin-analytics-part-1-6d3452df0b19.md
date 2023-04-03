# 免费比特币取证-第 1 部分

> 原文：<https://medium.com/coinmonks/free-bitcoin-analytics-part-1-6d3452df0b19?source=collection_archive---------0----------------------->

![](img/0949eaba5c3ebef0d2adbafe758dec62.png)

在过去的几年里，比特币取证和隐私在比特币和更广泛的加密生态系统中引起了越来越多的关注和争议。随着复杂的隐私工具(如 Wasabi、Samourai 和 Joinmarket 等 Coinjoin 钱包)以及复杂对手(如 Chainalysis 和执法部门)的崛起，可以说该领域已经取得了相当大的进步，并继续以极快的速度前进。在这篇文章中，我希望探索在不使用任何付费高级工具和没有任何编码技能或数学学位的情况下，我们能把比特币分析推进多远。简而言之，一个(相对)不太懂技术，但非常坚定的比特币用户能从比特币区块链中了解多少。我希望这篇博文不仅能教会你如何进行比特币取证，还能教会你为了保护隐私可以避免哪些错误。

这篇博文将分为 3 个部分，第一部分我将解释比特币分析的基础知识，第二部分我们将更深入地从技术角度探索比特币分析(仍然不需要编程技能)，最后在[第三部分](https://pagan404.medium.com/free-bitcoin-forensics-part-3-96351bcbff76)我们将进行模拟调查，将这里的一些信息付诸实践。

在我开始之前，我必须提到，尽管这篇博客很长，但它绝不是比特币取证的全面概述，事实是，比特币取证本身是一门非常广泛的科学，而且它还在不断发展。这篇文章写于 2020 年 12 月，所以随着时间的推移，其中的一些部分难免会过时。

最后，在开始之前，我必须指出，你需要对比特币交易有一个很好的理解，以充分利用这个博客。如果你还没有，我推荐你去看看《掌握比特币》这本书的交易章节(或者就去看看整本书，这本书很棒，而且是免费的):
[https://github . com/bitcoinbook/bitcoinbook/blob/develop/ch06 . asciidoc](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch06.asciidoc)

**个人交易基础**

首先，让我们了解一下基本的比特币交易以及从中可以得出的基本结论。在探索区块链时，您将会遇到的常见交易如下所示:

![](img/a53681fae42594d0af8a9eae028c3df3.png)

[https://blockchair.com/bitcoin/transaction/7a3fb18c76b3f54606b065209521f6a678692a592c8bcd7460d7dcf8d06faf19](https://blockchair.com/bitcoin/transaction/7a3fb18c76b3f54606b065209521f6a678692a592c8bcd7460d7dcf8d06faf19)

我们已经有了很多关于交易的信息，比如交易发生的时间、费用、涉及的地址、交易的规模等等！但是我们稍后将讨论拥有所有这些元数据的含义，现在，让我们看看仅从发送者/接收者(通常称为输入/输出)部分我们可以推断出什么:

![](img/190c4252900f897ef29ab51367a92f50.png)

仅从这一点，我们就可以得出几个重要的结论:

1.  所有发送者地址属于同一个人/实体。

除非该交易是 Coinjoin 或 Payjoin(并且它看起来不像 Coinjoin，也不太可能是 Payjoin ),否则所有发送者(输入)地址都属于同一个实体。这是一个非常安全的假设，因为比特币交易中的绝大多数输入属于一个实体。Coinjoins 和 Payjoins 故意打破这种假设，以提高发送者的隐私，但 payjoins 非常罕见，Coinjoins 很容易识别。

2.该事务有一个更改地址(它从 16a 开始…)。

我们可以做出的第二个重要假设是，该事务有一个也属于发送方的更改地址。在这种情况下，我们立即可以说更改地址是以 16a 开始的地址，因为该地址也出现在输入端，在其他事务中，更改地址可能不太明显，但有许多其他指示可以帮助您确定更改地址。

3.以 12e 开头的地址是目的地址。

因为我们知道以 16a 开头的地址是一个变化地址，这意味着以 12e 开头的地址只能是目的地址。发送者希望向其转移他们的部分硬币(确切地说是 0.16358750 BTC)。现在，当然，我们不能说这个地址是属于别人的，还是发件人的地址在他们控制的另一个钱包里，但我们可以说这是唯一一个实际上不属于发件人的地址。

做出所有这些假设后，我们可以将交易着色以表示所有权，其中红色标记的地址属于发送方，黄色标记的地址属于接收方:

![](img/ab36d6cd70f776bcdf16cb2f73d3b136.png)

尽管这是您可能遇到的最常见和最基本的事务组合之一，但这当然远远不是唯一的事务类型。事务可以有各种形状和大小，实际上没有什么限制一个事务可以有多少输入和输出。为了说明这一点，我从一个随机的块中随机挑选了一些事务，让我们看看我们可以从中确定什么:

![](img/a7e3a6d7c908a25eb92fe309609d8a89.png)

[https://blockchair.com/bitcoin/transaction/0ec7de5b781fe8bff7638dca5f40e8d157287ed83496ae96b430d34c12ec8490](https://blockchair.com/bitcoin/transaction/0ec7de5b781fe8bff7638dca5f40e8d157287ed83496ae96b430d34c12ec8490)

该交易缺少任何变更地址，这使得它很可能是一个“转移”，这意味着来自发送者地址的所有资金都被转移到另一个地址，该地址很可能也由发送者控制。一旦我们检查了寄件人的地址，我们事实上看到他们没有任何资金，这使得假设更有可能。

![](img/ee693b87a5ac217692121be3ece25fb2.png)

[https://blockchair.com/bitcoin/transaction/941b29236d767bd7bbc61c85574c891c18ac4ea5f514c0e76f38a3507fde9c0c](https://blockchair.com/bitcoin/transaction/941b29236d767bd7bbc61c85574c891c18ac4ea5f514c0e76f38a3507fde9c0c)

在此交易中，我们可以假设更改地址是接收者部分中以 3 开头的两个地址之一(这种地址类型称为 Pay-to-Script-Hash 或 P2SH)，而目的地址是以 1 开头的地址(这种地址类型称为 Pay-to-Public-Key-Hash 或 P2PKH)。加密钱包很少为改变创建不同类型的地址，通常，改变地址和输入是同一类型的。然而，确定哪个以 3 开头的地址是实际的更改地址要困难得多。

![](img/0ff62533dd249be477aa4148d5095d8b.png)

[https://blockchair.com/bitcoin/transaction/5d3c39bbd3d606c43e9146dcbf41d1d93b70023b68bf818e46b23fe71744bcaf](https://blockchair.com/bitcoin/transaction/5d3c39bbd3d606c43e9146dcbf41d1d93b70023b68bf818e46b23fe71744bcaf)

该事务不会因为使用多种地址类型而受到影响，但是由于输入地址与输出地址之一相同，所以很明显哪个是更改地址。如果没有地址重用，这个事务将是完全私有的(好吧，如果你也排除了变化输出比所有输出大得多的事实，这也是一个线索)。地址重用是危及隐私的最大罪过之一。

这种“投入少，产出少”的交易在区块链最为常见，但是，还有另外两种交易类型值得了解:

> 交换交易

![](img/77e2724a475eb155a65ec6ca2e46b357.png)

[https://blockchair.com/bitcoin/transaction/11a3c72f534e594a46e45eefa191f205d45f00d4a2b36104f0abe95df61182d4](https://blockchair.com/bitcoin/transaction/11a3c72f534e594a46e45eefa191f205d45f00d4a2b36104f0abe95df61182d4)

交易所交易通常很容易辨认，因为它们有大量的产出(有时也有投入)。交易所交易在某种程度上破坏了交易图，因为被发送的资金属于多个人，并且没有办法告诉交易所的哪个客户将资金发送到哪个地址。人们有时确实将交易所作为某种混合器，但当然，交易所本身存在一个很大的缺点，即知道用户在哪里提取了他们的资金(更不用说如果他们怀疑资金是非法获得的，可以冻结资金)。

有趣的是，地址 1 ndyjtntjmwk 5 xpnhjgamu 4 hdhigtobu 1s 属于币安交易所，这是币安自己承认的:

![](img/d0ac13d6fa47789a3e8bd05215b872f9.png)

[https://twitter.com/binance/status/961666467325358081?lang=en](https://twitter.com/binance/status/961666467325358081?lang=en)

查看一下可以看到很多兑换交易的例子:
[https://block chair . com/bit coin/address/1 ndyjtntjmwk 5 pnhjgamu 4 hdhigtobu 1s？_ type =地址&_ search =主页](https://blockchair.com/bitcoin/address/1NDyJtNTjmwk5xPNhjgAMu4HDHigtobu1s?_type=address&_search=homepage)

> 共同加入交易

![](img/87fee256bd462fc898c1d14396cea03b.png)

[https://blockchair.com/bitcoin/transaction/b3dcc5d68e7ba4946e8e7fec0207906fba89ccb4768112a25d6e6941f2e99d97](https://blockchair.com/bitcoin/transaction/b3dcc5d68e7ba4946e8e7fec0207906fba89ccb4768112a25d6e6941f2e99d97)

Coinjoins 初看起来与 exchange 事务相似，但它们的所有输出都具有相同或非常相似的数量(值得注意的是，并非所有 coinjoin 事务都具有如此高的输入和输出数量，但后面更多的是关于一种不太常见的 coinjoins 类型)。Coinjoins 真正打破了事务图，因为输入和输出属于不同的实体，如果 coinjoin 构造正确(并且 coinjoins 完成后涉及的实体不会出错)，则没有办法判断哪个输入与哪个输出相关联。

**有用的工具**

我在网上搜索了所有可能在法医调查中有用的免费工具。希望在您阅读本文时，它们仍然可以工作:d .如果您愿意，可以跳过这一部分，当您看到我在本文后面提到其中一个工具时，可以回来了解一些背景知识。

[https://blockchair.com/](https://blockchair.com/)—我最喜欢的普通 BTC·区块链浏览器，虽然乍一看它只是一个普通的浏览器，但它在 privacy-o-meter 工具中有非常强大的功能，可以自动确定可能的发件人、收件人和更改地址，它提供了大量关于地址的深入信息，例如它是否使用 RBF，它是否有见证数据，下一次或上一次交易的任何输入或输出都涉及到(这使得跟踪资金路径变得容易得多)，它甚至还有一个 API，供书呆子使用:)

您只需拨动此开关，即可开启隐私 o-meter 功能:

![](img/1a7c27b5f55e9f867984cfdc0d6e54e4.png)

它会给发送者、接收者涂上颜色，并用不同的颜色改变地址，并提供一些指示，说明它为什么会这样想:

![](img/934f4ee175385e23b112521d123d72ec.png)

当然，值得注意的是，你永远不要盲目相信 privacy-o-meter 或任何其他取证工具。像区块链分析中的一切一样，它建立在可能是错误的启发式(假设)之上。你可以在这里阅读启发式隐私测量仪使用的完整列表:【https://blockchair.com/api/docs#link_M6

[https://blockstream.info/](https://blockstream.info/)和[https://www.blockchain.com/explorer](https://www.blockchain.com/explorer)——你可以使用 Blockchair.com 的替代方案，它们将提供很多相同的信息，但更具技术性。Blockstream 和 Blockchain.info explorers 使读取交易签名变得容易，Blockstream 以类似于 Blockchain.info explorers 的方式提供交易的隐私故障(尽管它提供的信息较少)。当然，你也可以一起使用这些探索者。

[https://www.kycp.org/#/](https://www.kycp.org/#/)——kycp.org 是一个区块链探险家，专门用来尝试去匿名化 coinjoins，它是迄今为止最强大的免费工具，你可以用它来找到交易中输入和输出之间的联系，但它有一点学习曲线。解释 kycp.org 的全部力量远远超出了本博客的范围，但你可以在这里阅读 kycp.org 团队的解释:
[https://medium . com/samourai-wallet/known-your-coin-privacy-using-kycp-org-7b3b 4385 d8b](/samourai-wallet/knowing-your-coin-privacy-using-kycp-org-7b3b4385d8b)

[https://oxt.me/](https://oxt.me/)——当你从宏观角度探索你所跟踪的基金所走过的道路(所谓的交易图)时，这个探索者是你最好的朋友。Oxt.me 有一个令人惊叹的可视化工具，可以给你这种能力，还有一个钱包聚类功能，可以自动将地址聚类到共享钱包中。虽然查看单个交易很棒，但它是迄今为止钱包聚类和交易图可视化的最佳工具(实际上，据我所知，它是交易图可视化的唯一免费工具)。这在任何调查中都是必须的。

[https://www.walletexplorer.com/](https://www.walletexplorer.com/)—是一个钱包集群的替代浏览器，你可以用它来代替 oxt.me。Walletexplorer.com 创建于很久以前，总的来说，它在钱包集群方面不如 oxt . me，但它有很多历史数据(它缩进了 Mt. Gox 等旧的 exchange 钱包)，当然值得一试。

[https://learnmeabitcoin.com/tools/path/](https://learnmeabitcoin.com/tools/path/)——一个漂亮的小工具，可以告诉你两个比特币地址之间的最短路径，有时这个工具很慢，如果地址相距太远可能不可靠，但仍然有用。

[https://bitnodes.io/](https://bitnodes.io/)——本博客不会关注比特币网络监控，然而，如果你对此感兴趣，Bitnodes 网站可能是最好的起点，因为它通过找到网络中所有可到达的节点来绘制比特币网络。

[https://www.cryptotxalert.com/](https://www.cryptotxalert.com/)——如果你找到一个你希望关注的地址，你可以使用 cryptotxalert 网站确保每当有来往于该地址的活动时，你都会收到一封电子邮件。从技术上来说，这个网站是付费的(虽然它有几周的试用期)，但由于价格不高，任何人都可以使用这个网站，它没有很高的准入门槛。

**变更地址检测**

如前所述，检测更改地址并不总是很容易，但幸运的是，有许多线索可以帮助确定它(尽管有时也不可能):

*   地址重用——如前所述，地址重用对于您的隐私来说是可怕的，尤其是如果地址因变更而被重用。
*   不必要的输入—在下面的交易中，以 363…开头的地址很可能是更改地址，而以 1Kr…开头的地址是目的地，因为如果情况相反，交易就不需要那么多输入，一个输入就足够了(尽管有些钱包编程很差，无缘无故地接受不必要的输入，所以对此要有所保留):

![](img/9c2a7c1b7f48fc3136010463f83cb70a.png)

*   整数-如果其中一个输出是整数(比如正好是 1 BTC ),则它更有可能是目的地址。当以美元计价的金额是整数时，情况也是如此(比如说，产出在转移时正好值 100 美元)。
*   发送到不同的地址类型-大多数钱包将更改地址指定为与输入地址相同的类型，因此您可以排除与输入地址类型不匹配的输出。
*   钱包怪癖——有些钱包总是把零钱地址放在最前面、最后，或者以其他非随机的方式。
*   费用冲击—通常，费用将从更改地址输出中冲击。如果观察力敏锐的攻击者在费用增加之前和之后监视内存池，他们将能够发现哪个输出是变化的。
*   其中一个输出远大于另一个——指的是这样的情况:

![](img/afb55a4048041a9eb6265ea1644b5651.png)

[https://blockchair.com/bitcoin/transaction/bf6b6841085d799cf7225d3c1bef1edc854508e51c37e6ecd3f854b2238271b6](https://blockchair.com/bitcoin/transaction/bf6b6841085d799cf7225d3c1bef1edc854508e51c37e6ecd3f854b2238271b6)

*   即使没有重用更改地址，也很明显这是更改地址，因为输出比其他地址大得多。十有八九，其他产出是某种形式的支出。

这些(在我看来)是一些最重要的线索，但我相信还可以想到更多。这些线索中的一些比另一些更可信，最好确定您在区块链跟踪的实体的多次交易之间的模式。

**分析元数据**

区块链探险者提供了大量关于交易和地址的有用元数据。人们很容易忽略这些信息，只关注资金的流动，但这样可能会丢失重要的细节。例如，如果跟踪者注意他们所跟踪的实体的交易的时间戳，他们可能能够确定所讨论的实体运营所在的大致时区。类似地，如果追踪者注意交易的技术特征(例如费用大小、交易结构、脚本等)，他们可能能够确定实体正在使用什么钱包，因为一些加密货币钱包非常独特。一般来说，关注多个事务中的任何重复模式是有用的。

**识别地址**

最终，几乎所有调查的目标都将是确定所涉资金的流入或流出地址(或两者都有)。这样做是目前为止对目标进行去匿名化的最有效的方法，尤其是当被跟踪的实体将他们的资金发送到一个交换地址时。当然，对于警方的调查来说尤其如此，因为执法部门可以接触到交易所，并命令他们提供他们所掌握的有关嫌疑人的所有信息。这是如此重要，以至于在我看来，你可以做的改善你隐私的第一件事就是确保你以一种无 KYC 的方式获得和花费你的资金，例如，尝试通过点对点交易所如 LocalBitcoins 获得比特币，而不是常规的集中交易所。关于如何做到这一点的更多信息可以在这里找到:
[https://www . buybitcoinworld wide . com/kb/buy-bit coin-without-verification-id/](https://www.buybitcoinworldwide.com/kb/buy-bitcoin-without-verification-id/)

理解您遇到了一个交换地址并不困难，交换地址通常会有大量非常频繁的交易。这里有几个例子:

*   [https://block chair . com/bit coin/address/1 ndyjtntjmwk 5 pnhjgamu 4 hdhigtobu 1s](https://blockchair.com/bitcoin/address/1NDyJtNTjmwk5xPNhjgAMu4HDHigtobu1s)
*   [https://block chair . com/bit coin/address/3c bq 7 at 1 ty 8 kmxwlbitag 7 yt 6 bpbkchq 64](https://blockchair.com/bitcoin/address/3Cbq7aT1tY8kMxWLbitaG7yT6bPbKChq64)
*   [https://block chair . com/bit coin/address/12 cgpfdjvixbwhbhra 3 tuw 1 egnl 25 zqc3p](https://blockchair.com/bitcoin/address/12cgpFdJViXbwHbhrA3TuW1EGnL25Zqc3P)

确定它们属于哪个交易所是困难的部分。你可以尝试做几件事:

> 试着谷歌一下地址…

如果你试图识别的地址属于一个足够大的交易所，那么一定有人在互联网上的某个地方提到过这个地址。你可能并不总能幸运地从官方交易所账户中找到提到他们地址的推文:

![](img/d0ac13d6fa47789a3e8bd05215b872f9.png)

[https://twitter.com/binance/status/961666467325358081?lang=en](https://twitter.com/binance/status/961666467325358081?lang=en)

但是找到一个不满的 exchange 客户的 Reddit 帖子是完全现实的:

![](img/b900fc62b4dfea30f6a8b70fab223c54.png)

[https://www.reddit.com/r/CryptoCurrency/comments/6ind8l/bitcoin_address/](https://www.reddit.com/r/CryptoCurrency/comments/6ind8l/bitcoin_address/)

显然，Reddit 和其他社交媒体上的帖子没有交易所官方 Twitter 账户上的推文那么有力，但它可以成为坚持的东西。

当谷歌搜索地址时，您可能需要输入地址和“-block”命令。这将自动删除所有在 URL 中包含“block”一词的搜索结果，因此大多数区块链探险家只会显示地址，而不会提供任何相关信息。

> 尝试使用索引著名地址的块探索者…

一些区块链探险者实际上试图索引已知的交换地址和/或钱包，没有一个接近完成，但肯定值得搜索它们。这里有一些探险家-

*   [https://oxt.me/](https://oxt.me/)
*   【https://www.walletexplorer.com/ 号
*   【https://www.bitcoinabuse.com/ 
*   [https://bitinfocharts.com/](https://bitinfocharts.com/)
*   [https://vivigle.com/](https://vivigle.com/)
*   [https://checkbitcoinaddress.com/](https://checkbitcoinaddress.com/)

**Outro**

这是 3 部分系列的第 1 部分，在[下一部分](https://pagan404.medium.com/free-bitcoin-forensics-part-2-51bafd9e032a)中，我们将探索更复杂的概念，这些概念对了解比特币分析非常有帮助，例如地址格式、交易图、钱包猜测和指纹识别、coinjoins、payjoins 等等。

## 另外，阅读

*   [加密交易机器人](/coinmonks/whats-the-best-crypto-trading-bot-in-2020-top-8-bitcoin-trading-bot-c16adeb13317)
*   [Uniswap API](https://bitquery.io/blog/uniswap-pool-api) —如何获取 Uniswap 数据？
*   [AAX 交易所评论](/coinmonks/aax-exchange-review-2021-67c5ea09330c) |推荐代码、交易费用、利弊
*   [Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   [FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   [逐位交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   [Bitsgap vs 3 commas vs quad ency](https://blog.coincodecap.com/bitsgap-3commas-quadency)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [莱杰纳米 S vs 特雷佐 one vs 特雷佐 T vs 莱杰纳米 X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   [bits gap review](/coinmonks/bitsgap-review-a-crypto-trading-bot-that-makes-easy-money-a5d88a336df2)——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   [alt ready 审查](https://blog.coincodecap.com/altrady-reivew)
*   [埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)
*   [赛克斯石评论](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)
*   [BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |赚取高达 8.6%的加密利息
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)

> [直接在您的收件箱中获得最佳软件交易](/coinmonks/newsletters/coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://medium.com/coinmonks/newsletters/coinmonks)