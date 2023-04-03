# 在交易所之外使用 Oracle

> 原文：<https://medium.com/coinmonks/use-of-oracle-beyond-exchanges-1af3946d029?source=collection_archive---------2----------------------->

## *DeFi 审计需要财务人员&安全人员同处一室*

最近围绕[的 bZx 事件](/razor-network/flash-loan-attacks-and-how-to-avoid-them-7c53298a593)将整个 [DeFi](https://blog.coincodecap.com/tag/defi/) 生态系统推向了一个全新的维度。人们现在比以前更关心把钱放在一个分散的平台上。

真是讽刺。bZx 事件发生在一个分散的平台上，这个平台与一个流动性、杠杆和借贷的分散平台相连。尽管如此，price feed 完全来自一个单一的来源:Kyber(很可能是集中的端点)。事件是利用价格数据提供商在价格反馈方面对分散交易所(DEX)的依赖。它不仅导致了 Eth 的丢失，还在不信任的生态系统中产生了信任问题。

**一个** [**聪明的契约**](https://blog.coincodecap.com/tag/smart-contact/) **同时是聪明和愚蠢两者**。它只会做被允许做的事情。如果它被编码为从单一来源获取数据，那么它将从单一来源获取数据。不管最终的结果是什么。

![](img/08ca606fe8ff57fb0d2f5b53a9a5312c.png)

pic credit: [https://unsplash.com/photos/qwtCeJ5cLYs](https://unsplash.com/photos/qwtCeJ5cLYs)

# 如何消除这类问题的风险？

我们可以从多个真实或参考来源获取数据，而不是从单一的真实来源获取数据。当以正确的方式从系统外部和体系结构中获取数据时，很难独立地对整个系统进行编码。Oracle 有助于解决这些问题。

**在降低资产损失风险方面，数据提供商的来源是安全审计的主要组成部分之一。**

# 围绕它有哪些可能的解决方案？

有几个项目，如 Razor Network、ChainLink、Band Protocol 和 UMA，正在以多种方式解决 oracle 在智能合约中的攻击向量攻击或数据馈送问题。

[剃刀](https://stealth.razor.network/)是**的一个完全去中心化的神谕**其运作方式是既**自动化&手动**。用户可以灵活选择他们希望解决问题的速度。一些用例，如递归预测市场围绕体育，这需要更快的解决方案最适合剃刀。它使用的 [Schellingcoin 机制](https://blog.ethereum.org/2014/03/28/schellingcoin-a-minimal-trust-universal-data-feed/)，该机制对各种博弈论攻击具有鲁棒性，并且具有快速响应时间，使其适合 DeFi 应用。以太坊博客上有一篇很漂亮的文章。

还建议使用 **Neo oracle** 作为插件。它基本上是其他 oracle 提供商(Razor Network、Chainlink、Band、UMA 等)的 oracle 价值中值。这被认为是一个更好的解决方案。但同样，就做决定的时机而言，有一个权衡。当我们向他们提供来自多个真实来源的数据时，由于各种依赖性，通常会很慢。它需要足够快速、自动化和智能来做出决策。最好的用例是二元交易，在这种交易中，买入/卖出发生在现货价格上，价格也需要足够分散和智能。

***免责声明:*** *此处呈现的所有内容均为研究型，仅供教育之用。请不要把这篇文章当成正式的投资建议。*

**跟着我上**👇

[推特](https://twitter.com/a4nkit) | [领英](https://www.linkedin.com/in/ankitrajjha/)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)[![](img/e9dbce386c4f90837b5db529a4c87766.png)](https://coincodecap.com)