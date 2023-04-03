# 比特币季节性波动

> 原文：<https://medium.com/coinmonks/bitcoin-seasonal-fluctuations-8c59e18f3fe7?source=collection_archive---------0----------------------->

最近，人们对比特币网络上所谓的季节性采矿波动争论不休。故事是这样的，每年八月到十月左右，随着中国雨季的停止，廉价而丰富的水电资源逐渐枯竭。这迫使许多效率低下的新型矿工关闭或转移到其他地方寻找更负担得起、更容易获得的能源——如果你愿意，创造迁移或游牧矿工。

该叙述还声称，该网络在每年的时间间隔内看到散列率和困难的显著下降，大致符合中国水力发电的季节性下降。现在，也就是 2020 年秋季，这种情况肯定会出现，因为许多人推测，最近每秒约 48 万亿次散列(Eh/s)(占网络总散列率的 30%)的损失正是由于这种现象。但是其他年份的数据支持这一点吗？

而最近的[比特币](https://blog.coincodecap.com/a-candid-explanation-of-bitcoin)在块高[655200](https://blockstream.info/block/0000000000000000000be6439455cefcb61e62eb9b0f47f2c99d2de2ce8dd144)的难度调整，比特币历史上最大跌幅之一呢？Clark Moody 的仪表盘显示，基于前面提到的网络散列率损失，阻塞难度下降了 16%。

# 块生产率

[比特币](https://bitcoin.org/bitcoin.pdf)协议针对某些可预测的结果进行了微调和优化。网络达到这些期望结果的方式是由于一系列精心设计的系统规则和指导方针，这些规则和指导方针在自由和开放源码软件创建时就被精心制作。比特币时间链是一系列的[区块](https://en.bitcoinwiki.org/wiki/Block)，根据预设的一系列规则对交易进行验证、分组和排序。一个这样的规则是，数据块以大约每 10 分钟、每小时 6 个数据块、每天大约 144 个的编程速率添加到链中。

# 阻止难度调整

区块难度通常与矿工生产一个区块所需的计算工作量成正比。[比特币创世纪方块](https://en.bitcoin.it/wiki/Genesis_block)的难度为 1，今天的方块难度为 19997335994446。这意味着今天发现一个区块比发现第一个区块要困难 20 万亿倍。区块难度是一个无单位的比特币网络度量。

为了保持 10 分钟的区块生产率，不断变化的矿工数量和完成的工作，软件调整[区块难度](https://en.bitcoinwiki.org/wiki/Difficulty_in_Mining)——每 2016 个区块，或大约每两周一次——通常被称为比特币区块难度时期。这种难度调整算法即使在网络散列率剧烈波动的情况下也能保持平均块生产率。随着时间的推移，随着越来越多的矿在网络上失去运气，块难度已经自动向上调整，以补偿和稳定块生产率。

![](img/a9becfba32dc4819c007a9f02cb8b51e.png)

Bitcoin network hash rate and difficulty history, linearly.

在上面的图表中，可以看到，在散列率降低后，难度经常会下降，而随着散列率的上升，难度会增加。如果平均而言，块的铸造速度快于(或慢于)10 分钟，这将意味着更多(或更少)的计算能力被指向比特币，超出了难度阈值所能容纳的范围。随着更多或更少的矿工朝着区块链工作，区块难度[目标](https://en.bitcoin.it/wiki/Target)数字将被改变以进行补偿，确保以大约每十分钟一个的速度创建区块。

![](img/4f4187cde3081d713b1e3f038cd15fee.png)

Bitcoin network hash rate and difficulty.

虽然我们可以清楚地看到难度在线性图表上降低，但是，在上面的对数图表上，散列率和难度的降低不太明显。历史上，在比特币网络上，区块难度呈上升趋势，区块难度很少降低。这部分是由于采矿设备[效率和有效性](https://bitcoinmagazine.com/articles/introducing-cbpi-a-new-way-to-measure-bitcoin-network-electrical-consumption)的提高。

# 比特币网络散列率

在过去的十年中，只有少数几个月的阻滞难度值低于起始值。如果视图是按月和按年的平均比特币网络散列率，这就更明显了。年复一年，比特币网络散列率没有一个月下降。

![](img/dbfc21c44b6446865f9b2cf7deaf547d.png)![](img/ee1af8405e3f76a64b1ae627de2d336b.png)![](img/6b825ec278cc0c09dfa9f4b82a5fe9f2.png)

# 季节性波动？

那么，既然我们已经确定，在足够长的时间框架内，比特币的散列率会大幅上升，那么它们对季节性波动的有效性呢？如上所示，2020 年、2019 年和 2018 年的平均网络散列率在年底都低于夏末秋初。其他年份呢？

## 2013 年秋季

对于 2013 年，该网络似乎没有向下调整难度。这种积极向上的趋势可能是由于在此期间发生的 ASIC 效率革命。

![](img/646dc0890ed958c4325bb4b394a006b2.png)

## 2014 年秋季

对于 2014 年，在 11 月下旬的时间框架内有一些难度向下调整，但是，在赛季的大部分时间里，难度似乎呈上升趋势。

![](img/e60bd6677396af82eaf293ffe4acd5f9.png)

## 2015 年秋季

2015 年的情况与 2013 年类似，电视网似乎没有出现难度下调。更多更好的 ASICs 仍在开发中。

![](img/526a876fe214a531f33becc350fab41f.png)

## 2016 年秋季

因此，2016 年在 10 月份左右会有一个小的下调难度。此外，在同一时间段内，网络哈希速率和难度的增长速度似乎有所放缓。

![](img/dea050780e7204c9169e78cb4b67b6fe.png)

## 2017 年秋季

2017 年讲述了一个与 2016 年类似的故事，网络哈希率增长停滞，难度实际上向下调整了几次。这一点尤其值得注意，因为价格在同一时间线内大幅上涨。

![](img/5fb1e75a415120eff3f7209b7b8949f5.png)

## 2018 年秋季

2018 年秋季可能是全网难度和哈希下降最明显的季节性趋势。值得注意的是，在这些时间段内，价格也从历史高点下跌。网络的很大一部分，几乎一半，季节性的离线。然而，由于难度调整算法的优雅，对等网络继续向前翻腾。

![](img/8415b237a27c2a02c1ec07302dd88d14.png)

## 2019 年秋季

2019 年秋季没有像 2018 年赛季那样明显下降，但它确实显示了一些难度和杂凑率的降低。它还显示了在相同的时间范围内网络散列功率增长的类似阻碍。

![](img/a52b5c5fce99f85fd4f601d124550232.png)

## 2020 年秋季

因此，这就把我们带到了今天，许多中国 ASICs 关闭的叙事似乎在 2020 年秋季仍然有效。还有什么地方会有 48 Eh/s(网络的 30%)的人无所事事地等待着丰富和负担得起的能源呢？这相当于大约 300 万个 ASIC 采矿单位。

![](img/0a918f4223d9f8db0e4d37158e37ebec.png)

那么，在 10 月和 11 月的时间框架内，这个下降在以前的难度下降中排在什么位置呢？通过环比数据，我们实际上可以看到 2011 年有一个更大的月环比下降，但它发生在几个不同的难度调整上。2020 年 11 月的难度下降是近年来我们看到的最大一次，原因是有进取心的中国水电矿商明显的季节性波动。从 2012 年到 2015 年，这几个月的难度没有下降。

![](img/939bf80d09e2394460839c8ee6a1b84c.png)

这些季节性难度和散列率波动如何叠加到比特币网络的整体交易历史中？难度调整和散列率变化的[直方图](https://en.wikipedia.org/wiki/Histogram)。

![](img/913ff35a78110483c24b8ba1e60d1cd1.png)![](img/7c289f0d3ad52c777f7e6dbaf01ddad2.png)

# 比特币挖矿死亡螺旋？

当比特币的网络散列率或价格下降时，总会有很多人猜测这种可能性，即俗称的“采矿死亡螺旋”。这种说法认为，如果价格下跌，足够多的矿工将关闭，网络将失去其散列率的相当大的百分比。这将迫使一些矿商清算他们的收益，推低市场价格，并进一步加剧这种恶性反馈循环，直到死亡螺旋随之而来。对于比特币这样的网络来说，这将导致矿工关闭，区块停止开采，时间链停止传播——比特币将失败。然而，作为一个例子，Dogecoin $DOGE 仍然在制造块，所以这个理论可能并不适用于每个系统。有许多狂热分子、狂热分子和“最后的矿工”会仅仅为了维护这些系统和信仰的信念而维护其中一些系统。

因此，让我们想象一下，如果 50%的矿工在难度调整时停止采矿并关闭；会发生什么？剩余的一半网络需要两倍的时间来找到 2016 个块。这意味着 4 周，或者大约 1 个月，到达下一个难度调整点。对网络会有什么影响？[内存池](https://en.bitcoin.it/wiki/Satoshi_Client_Transaction_Exchange)将开始建立，交易将会延迟，费用将会增加，因为人们会出价购买新的甚至更稀缺的块空间。这实际上正是我们在散列率最近下降后几天所看到的。然而，比特币网络最终调整了难度，就像过去每次出现第 2016 个区块时一样。对于比特币网络面临的一些不同挑战，这种难度调整算法是一种非常优雅的解决方案，由于这些类型的解决方案，系统可以自我调节。

## 另外，阅读

*   最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   [FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   [Bybit 交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [莱杰 Nano S vs 特雷佐 one vs 特雷佐 T vs 莱杰 Nano X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   [bits gap review](/coinmonks/bitsgap-review-a-crypto-trading-bot-that-makes-easy-money-a5d88a336df2)——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   哈森在线评论享受九折优惠
*   Bitmex 的[保证金交易指南](/coinmonks/the-idiots-guide-to-margin-trading-on-bitmex-dbbd7742c6fc?source=friends_link&sk=7bfa99d2a181142510c8442c8ddb0786)
*   [eToro 评论](/coinmonks/etoro-review-78807ddeb33c) |交易股票、密码、交易所交易基金、差价合约和商品
*   [BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |赚取高达 8.6%的加密利息
*   开发人员的最佳加密 API
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)

> [在您的收件箱中直接获得最佳软件交易](/coinmonks/newsletters/coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://medium.com/coinmonks/newsletters/coinmonks)