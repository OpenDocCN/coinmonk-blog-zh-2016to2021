# 物理交付及其对数字资产的意义

> 原文：<https://medium.com/coinmonks/physical-delivery-and-what-it-means-for-digital-assets-5daf6e9b2e68?source=collection_archive---------4----------------------->

![](img/4171e78795f117dae7c51c562484d3da.png)

Photo by [Waldemar Brandt](https://unsplash.com/@waldemarbrandt67w?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/oil-barrels?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

如果加密衍生品没有收敛风险会怎样？

如果你曾想知道衍生品世界中“现金结算”和“实物交割”的区别，2020 年是这方面的速成班。是的，这是油价下跌的一年，许多人会痛苦地记得那些日子。如果 Y2K 是为了修正日期格式中的一个微不足道的捷径，你可以打赌软件开发者和风险管理者在他们的系统和场景中从来没有为油价下跌做好准备。

到底是关于什么的？一如既往，当一个情况或多或少“不可能”时，有几个[汇聚因素](https://www.ft.com/content/b474224a-84bd-4824-b4d3-4824463d5015)到达不可能。 [CFTC 报告](https://www.cftc.gov/PressRoom/PressReleases/8315-20)列举了三个:2020 年初全球原油市场已经供应过剩，新冠肺炎导致需求空前减少；以及对全球原油储量的担忧。

但是存储空间和这有什么关系呢？缺失的一环是，石油产品交易实际上是“实物交割”期货。也就是说，当期货到期时，买方*实际上*收到固定数量的桶。此外，为了使市场标准化，只在特定地点接受交货([参见例如纽约商品交易所](https://www.cmegroup.com/trading/energy/crude-oil/light-sweet-crude_contract_specifications.html)原油的“交货程序”一段)。石油流动的任何中断和储存能力很容易饱和。今年 3 月，情况正是如此:需求的突然崩溃导致买家撤离。配备了足够设施的市场参与者可以收集交货，并通过保持其库存在原位来等待交货。然而，对于没有适当装备的参与者来说，持有期货合同意味着提货，而这是根本不可能的，因为没有指定设施的存储。在临近到期的日子里，例行交易石油而不考虑实际库存的投机者和交易员陷入了“甩卖”螺旋，将价格推至未知领域。

在这一点上，你可能会得出这样的结论:实物交付是一件讨厌的事情。不完全是。首先，应该指出的是，就商品市场而言，实物交割是买卖双方交易的最自然的结果。事实上，“现金结算”是不合适的:它鼓励投机，而不考虑交易产品的地理位置。实物交割还有另一个主要优势:它迫使衍生品的到期价格与其基础价格趋同。

例如，考虑一只股票的期货，到期时现金结算。期货的最终参考价是股票的收盘价(美国也是如此)。如果你以 100 美元买入期货，到期时标的股票以 110 美元收盘，你将收到 10 美元作为最后的追加保证金通知，这就是交易的结束。如果期货是实物交割，你将在到期时收到股票，最终价格是 110 美元，这是你必须支付的价格。对你有利的 10 美元追加保证金意味着你实际上是以 100 美元买入股票的——这是有道理的，因为这是你建仓时的期货价格。

在现金结算衍生品的世界里，到期价格是由基础资产的参考价格决定的，但这并不意味着你可以以这个价格收购基础资产。这两种价格之间可能会有差异。在实物交割的世界里，这是不可能的——你*会以到期价格*自动获得标的资产的所有权。

具体来说，我们以 BTC 为例，特别是 CME BTC 期货。期货到期日为 [CME 比特币参考汇率(BRR)](https://www.cmegroup.com/confluence/display/EPICSANDBOX/Bitcoin) ，定义为“[特定一小时计算窗口](https://www.cmegroup.com/education/articles-and-reports/analysis-of-cme-cf-bitcoin-reference-rate.html)内主要比特币现货交易所已执行交易流量的汇总”。未来是现金结算的。到期后，CME 将根据其 BRR 方法计算价格，并使用该价格结算最终的追加保证金通知。问题是:你如何复制这个价格？如果你持有比特币的 delta 中性头寸(多头 BTC，空头期货)，到期时你需要以与期货完全相同的价格平仓现金 BTC 部分。这意味着你需要以 BRR 的价格出售你的 BTC，不多也不少。这是一个雄心勃勃的提议:它意味着我/你连接到所有相关的交易所，ii/你精通技术，足以复制 BRR 实时交易所分配，iii/你可以在每个交易所以正确的价格在正确的时间下单并收到执行。如果 BTC 期货被实物交割，你将完全无所事事——你将自动获得交割 BTC 期货的收益，作为短期期货的一部分。

在传统市场，这是一个众所周知的问题，所有以“外来”参考为基础进行现金结算的衍生品都会出现这个问题。例如在法国，CAC 40 指数期货以 20 分钟的平均价格结算([“交易所交割结算价”](https://live.euronext.com/fr/product/index-futures/FCE-DPAR/contract-specification))。EDSP 的可交易性对所有参与者来说都是一个问题，即使是那些拥有技术装备的参与者也面临着趋同风险。当结算价格通过拍卖设定时，这种风险会大大降低，因为拍卖的基本机制意味着每个人都以相同的价格获得充分服务，否则拍卖不会结束。BTC 或其它数字资产没有这种情况:没有收盘拍卖，没有实物交割，这意味着未来的交易者必须应对不可预测的趋同风险。

现在你可能想知道…这有什么关系？毕竟，这不会阻止交易，也不会影响与数字资产相关的整体风险水平。嗯，它确实影响了数字行业引入担保贷款的能力，并通过它降低了信贷杠杆。[我在](/datadriveninvestor/the-most-important-financial-formula-of-all-part-ii-187d8274b7af)之前已经解释了期货合约的收敛公式是如何实现市场和信用风险之间的解耦的。然而，这只有在收敛确定的情况下才有可能。否则，担保贷款会带来市场风险，这对于追求收益的贷款人来说可能是不可接受的。实物交割将使担保贷款更加安全，进而为证券融资这一真正的新篇章铺平道路。

*关于我们* : [孙祖实验室](https://sunzulab.com)是一家领先的独立的流动性分析提供商，面向已经活跃或对密码好奇的投资者。我们想听听你的意见！！请通过[contact@sunzulab.com](mailto:contact@sunzulab.com)联系我们，或者访问我们的[研究页面](https://sunzulab.com/research)这里！

> 加入 coin monks[Telegram group](https://t.me/joinchat/EPmjKpNYwRMsBI4p)学习加密交易和投资

## 另外，阅读

*   最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [印度比特币交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [比特币储蓄账户](/coinmonks/bitcoin-savings-account-e65b13f92451)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [电网交易](https://blog.coincodecap.com/grid-trading)
*   [最佳密码交易所](/coinmonks/crypto-exchange-dd2f9d6f3769)