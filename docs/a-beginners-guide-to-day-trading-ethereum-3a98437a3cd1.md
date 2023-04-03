# 日间交易以太坊初学者指南

> 原文：<https://medium.com/coinmonks/a-beginners-guide-to-day-trading-ethereum-3a98437a3cd1?source=collection_archive---------0----------------------->

以太坊是仅次于比特币的第二大流行加密货币。每天有数十万以太币交易，相当于平均约三到四亿美元(每天！).由于其规模和受欢迎程度，它仍然像比特币本身一样不可预测和不稳定。但对我们来说幸运的是，日内交易密码背后有相当多的科学知识，这让我们可以做出更好的决策。

![](img/f58eff68d928aa1ab8c81e2a789dfaf0.png)

ETH-USD market snapshot from Binance with hourly candlesticks.

从上面的蜡烛图中，我们清楚地看到以太坊是如何从 1 月份的最高点(1440 美元/ETH)上涨到今天的 450 美元/ETH 以上的。尽管它的价值在下降，但在这个市场上有很多钱可以利用。

> 另请阅读:[最佳加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)

## 什么时候应该买卖以太坊？

在我们之前的文章中([这里](/coinmonks/a-profitable-litecoin-trading-strategy-dd044a39b521))我对莱特币(LTC)做了类似的分析。当我在为以太坊准备这个分析时，我注意到硬币之间有几个非常有趣的相似之处。所以如果你没有看过我之前的帖子，要么现在看，要么看完这篇，顺序无所谓。

> 这是使用基本数学的基本分析。尽管它很简单，但它是一个非常强大的方法，更重要的是，它非常简单，我们提出的策略甚至可以被一个孩子学会。

该分析基于使用一天中每小时以太币的平均价格(以美元计)。下面的雷达图显示了以太坊价格在平均一天内的变化情况——该数据是基于 3 个月的数据(1 月、2 月和 3 月)构建的。外环上的标签(0 到 24)代表一天中的 24 小时。而环/圈本身代表平均价格——注意最小的环代表最低价格(852 美元)，而外环代表最高价格(870 美元)。

![](img/368e2675d511dcc77048f6a417b35744.png)

Per-hour average price of Ethereum over a 3-month period

从这张图表中，我们了解到以下情况:

*   ETH 的最低均价在 UTC/GMT 19:00(下午 7 点)。
*   而最高值通常出现在早上 6 点或 7 点。
*   这个基于小时的价格图看起来和我在上一篇文章中展示的 Litecoin 非常相似。

从上面的图表中我们了解到，如果我们在最低点买入以太坊，然后在最高点卖出，那么我们就会盈利。让我们计算一下我们平均能赚多少利润:最低价格约为 858 美元/ETH，最高价格为 868 美元/ETH，因此低买高卖的投资回报率为:投资回报率=(868–858)/858 = 1.16%。这意味着，如果我们遵循这一策略，我们每天将获得大约 1%的投资回报率，因为这一数据跨越了 3 个月，我们可以计算出 1000 美元的投资在 90 天后的价值:$1000*1.01⁹⁰= 2448.63 美元——这是 144.86%的**投资回报率。**

现实从来没有这么简单明了，否则每个人都会因为交易密码而变得富有和出名。雷达图只显示一天中每小时的计算平均价格，但这并不意味着每天晚上 7 点左右价格最低，早上 6 点或 7 点价格最高。有些日子，图表可能看起来与上面完全不同，所以如果你虔诚地遵循这个策略，这些日子的投资回报率将是负的。

我更感兴趣的是回溯测试我们的策略。回溯测试是模拟我们的策略的一种方式，以模拟现实生活中的日交易场景，包括所有交易费用。我们模拟的最终结果将告诉我们我们赚了多少利润/损失。因为我已经实现了自己的回溯测试代码，所以我只需要调整交易策略。我们上述策略的投资回报率(**晚上 7 点买入，早上 6 点**卖出)在 90 天(3 个月)内产生了 22.10% 的**投资回报率，相当于每天 0.25%的投资回报率。这一点都不差，但与我们理论上的 144.86%的投资回报率相去甚远。**

为了理解为什么这个更高的投资回报率比超过 140%的乌托邦投资回报率更现实、更低，我们必须将数据分解成更短的时间段。让我们按月细分，而不是汇总 3 个月的数据:

**一月:**

![](img/052a6ededf472743d4f2f6156e67c052.png)

January per-hour average price of ETH

**二月:**

![](img/e02f466f9b3bb6d974c32887aedbaf59.png)

February per-hour average price of ETH

**三月:**

![](img/2bb802f3e0069b67cc2b1d7ba0a4c4f4.png)

March per-hour average price of ETH

再一次，这些图形的形状看起来和我上一篇文章中 Litecoin 的形状非常相似。然而，让我们继续关注分析——我们看到每个图表的形状看起来完全不同，没有月份是相同的。因此，我们的策略“晚上 7 点买入，早上 6 点卖出”并不是这些月度图表中的最佳策略，因为我们可以找到一个更好的策略来分别在每个月买入/卖出。原因是市场总是在变化。

## 常识

然而，我们在回溯测试中使用的策略产生了大约%22 的正投资回报率，实际上，如果由一个人来做决定并运用一点常识，它会表现得更好。一个有知识的交易者通常不会以低于他/她买入价的价格卖出(除非在一些特定的情况下)，但是我们愚蠢的策略除了服从我们的命令没有其他选择。

## 购买比率

影响我们投资回报率的另一个因素是“购买比率”。我的回溯测试策略有一个变量/参数，它指示应该用多少钱(美元)来购买某项资产，在我们的例子中是以太坊。如果它以 1 万美元开始，那么它可以选择是用这笔钱的 100%购买 ETH，还是只用其中的 10%。在我们的分析中，我们按小时计算了 ETH 的平均价格，但在一个小时内，价格可能波动很大。强迫我们的系统只在一小时的开始买入可能是一个次优的决定(这是当买入比率是 100%的时候)。

然而，如果我们将比率改为 10%，那么它将在一小时内只进行六次购买——原因是我使用 10 分钟的时间间隔；在这个特定的例子中，每步的时间是十分钟。也就是说，系统每十分钟就会决定是买进、卖出还是什么都不做。例如，在我们的例子中，如果时间的小时等于晚上 7 点，这意味着它将以该区间的价格发出“买入”指令。

再一次(就像我之前的文章一样)我们偶然发现了“我们应该为买入比率选择什么值”这个问题。让我们针对许多不同的购买率(从 5%到 100%开始)运行回溯测试模拟，而不是仅仅选择一个随机值。在下面的图表中，我画出了结果:

![](img/e7d2ac041bff8b7efef9783467ec19a8.png)

ROI % (Y-axis vertical) return from the back test simulation versus the Buy Ratio % (X-axis horizontal)

在上面的图表中有四个不同的图。每个月(一月、二月和三月)都有一个，但三个月加起来也有一个。有趣的是，投资回报率随着买入比例的上升而上升。因此，在这种情况下，最高的投资回报率是 100%的购买率。这也有点道理，因为我做的“买入”交易越多，我们要支付的交易费用就越多:对于较低的买入率，我们最终会做更多的“买入”交易，从而支付更多的费用。

另一件有趣的事情是，我们的投资回报率为正的唯一月份是一月，而二月和三月实际上导致了负的投资回报率。这意味着我们的策略在一月份会奏效，但在二月份和三月份都不会。对此有一个解释:在我们的 3 个月小时价格图中，1 月的价格明显高于 2 月和 3 月，所以这就是为什么结果更有利于 1 月而不是 2 月/3 月-即策略偏向 1 月。为了真正解决这个问题，我们可以计算每个月的标准每小时价格平均值，然后计算这些子结果的平均值。这不是我在这篇文章中要探讨的，而是在下一篇文章中。

![](img/2bb802f3e0069b67cc2b1d7ba0a4c4f4.png)

Radar chart of ETH’s average price-per-hour for March.

我们实际上可以为 2 月和 3 月构建一个类似的策略，然后通过简单地改变它应该买入和卖出的时间，使它的投资回报率为正。为了说明这一点，我根据 3 月份的数据构建了一个基于平均每小时价格的新策略，在这种情况下，它将在晚上 8 点(20:00)买入，然后在午夜(0:00)或凌晨 1 点(1:00)卖出。请看下面的结果:

![](img/7b37ec68efee350b325b018b77e7df5d.png)

ROI % (Y-axis vertical) return from the back test simulation versus the Buy Ratio % (X-axis horizontal)

在上面的结果中，我们清楚地看到，是在午夜还是凌晨 1 点卖出有很大的不同。

## 摘要

我们已经知道，我们可以在加密市场(以太币和莱特币)中发现一些每日和每小时的模式。然而，由于市场的不断发展，这些都不会一成不变。如果你想在交易中使用这些概念，你必须有一个每分钟都在更新的系统，就像自动更新雷达图一样。但是虔诚地使用这个策略还是有风险的——无论过去 X 天发生了什么，都不一定对未来的决策有用。换句话说，这种策略没有未来可预测的特性。然而，对于初学者来说，这确实是一个很好的学习和使用策略，因为正如我们通过回溯测试所证明的那样，一个人可以获得不错的投资回报，但是你不应该完全依赖它。

感谢阅读:)
-伊利亚

## **同样，阅读**

*   [顶级 DeFi 项目](/coinmonks/defi-future-10-promising-projects-in-the-defi-world-ff2b697ab006)
*   [最佳加密交易机器人](/coinmonks/whats-the-best-crypto-trading-bot-in-2020-top-8-bitcoin-trading-bot-c16adeb13317)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   [unis WAP 最佳钱包](/coinmonks/best-wallets-to-use-uniswap-e91a6385d9e8)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [bits gap review](https://blog.coincodecap.com/bitsgap-review)——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [3 商业评论](https://blog.coincodecap.com/3commas-review-an-excellent-crypto-trading-bot) |一款优秀的密码交易机器人
*   [3Commas vs Cryptohopper](/coinmonks/cryptohopper-vs-3commas-vs-shrimpy-a2c16095b8fe)
*   Bitmex 的[保证金交易指南](/coinmonks/the-idiots-guide-to-margin-trading-on-bitmex-dbbd7742c6fc?source=friends_link&sk=7bfa99d2a181142510c8442c8ddb0786)
*   加密摇摆交易的权威指南
*   [Bitmex 高级保证金交易指南](/coinmonks/bitmex-advanced-margin-trading-guide-2270c195ce25?source=friends_link&sk=1d986cca731f5084b9a2db4a4bc4a7ad)
*   [开发者最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com?utm_source=coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)