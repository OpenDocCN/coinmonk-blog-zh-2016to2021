# 季节性研究。

> 原文：<https://medium.com/coinmonks/decred-price-a-seasonality-study-414bac98bc99?source=collection_archive---------0----------------------->

## 查看每日和每月的 DCR 回报模式

![](img/57d34ec8dab00408300c6b11a9c29aed.png)

> 这篇文章是一篇关于[下降](https://decred.org/pt/)的季节性的论文的简化版，可在[这里](https://drive.google.com/open?id=1_k7sDYiFPsiwBV_jA6_-69nwm_Cij0qA)获得。我要感谢 [Richard Red](https://medium.com/u/9ee466259862?source=post_page-----414bac98bc99--------------------------------) 在写这篇文章时的反馈。找我 [@itsoneanalyst](https://twitter.com/itsoneanalyst) 。

# TL；速度三角形定位法(dead reckoning)

*   **季节性效应**在 20 世纪 80 年代，传统金融资产回报(如股票、货币、商品、外汇)激增。
*   最近的研究探索了比特币作为最受欢迎的加密货币的这些影响。然而，通过调整 PoS 和 PoW 功能来提供更好性能的新加密货币**和更新的哈希函数**如**decreed**尚未成为金融文献的目标。
*   我研究了 DCR 每日回报的星期效应和月份效应，也就是说，如果一年中一周/一月的任何一天/几天出现异常的高/低回报。
*   我发现**在 10%的水平上，周六的全部样本的平均回报具有统计显著性，而一周中的其他日子在年度周期中具有统计显著性。**
*   在整个样本中，我没有发现任何月份效应。
*   在没有交易成本和流动性风险的理论场景**中，我根据**过去的收益**提出了 **4 种策略**。**

# 1.介绍

## 最初关注比特币

自 [Nakamoto (2008)](https://bitcoin.org/bitcoin.pdf) 推出去中心化货币协议比特币以来，加密货币是一个新颖的研究主题。比特币提供了一种点对点的分散货币，这与在中央银行管理下的传统法定货币(如美元)形成了对比。此后，许多新的加密货币探索了安全、快速和保密交易的能力。

## 法令:更好的解决方案

提供这些功能的加密货币之一是 **Decred(股票代码:DCR)** 。与比特币不同，Decred 提供了 PoW 和 PoS 协议的混合解决方案。

通过不仅仅基于其中一种协议并采用更高性能的散列(Blake256)，Decred 可以利用这种灵活性来提供更完整的解决方案。正因为如此，Decred 在世界范围内被越来越多的采用，在发布时是市场上最大的加密货币[**# 38**](https://coinmarketcap.com/currencies/decred/)。

## 季节性分析

在传统金融市场(如股票、商品、外汇)中已经研究了月份效应、星期效应和周末效应。在加密方面，最近的大多数金融研究都集中在比特币上。**本文旨在填补金融研究中关于衰退和季节性效应的空白。**

**金融资产回报的异常发生在一周中的某一天、一年中的某一个月，或者周末之后**(反映在周一)引发了关于有效市场假说的问题( [Fama，1970)](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1540-6261.1970.tb00518.x) 。加密货币市场始终开放的事实及其独特的特征可能会导致与传统市场不同的影响。

## 因此，本文旨在回答**以下问题**:

1.  decredd(DCR)的收益在一周中的任何一天有异常值吗？
2.  Decred(DCR)的返回显示一年中的一个月的影响吗？
3.  如果这些效应存在，投资者会根据这些机会制定策略吗？

# 2.背景研究

## 市场有效吗？

市场效率假说(Fama，1970)认为**证券价格完全包含了投资者可获得的所有信息**，该假说多年来一直在传统市场中得到检验。对这一理论提出质疑的市场异常现象之一是周末效应，它出现在一系列关于股票、货币或外汇的综合研究中。

最初， [French (1980)](https://www.sciencedirect.com/science/article/abs/pii/0304405X80900215) 发现周一的股票收益**为负，比其他日子的收益**多 3 倍。周一的负回报后来被发现减少了，随着市场的成熟，这些异常现象被认为会消失。

## 加密货币的季节性

[栗原和福岛(2017)](http://www.scienpress.com/Upload/JAFB/Vol%207_3_4.pdf) 发现**周四****表现出最高的平均回报(统计意义上的)**并提出了市场变得更有效率的久而久之的观点。****

**更近的研究如 [Decourt、Chohan 和 Perugini (2019)](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3435176) 显示了比特币在其他日子的回报异常:**周二(最高平均回报)**，其次是周三和周六。**

**[Baur 等人(2019)](https://pdf.sciencedirectassets.com/273054/1-s2.0-S1544612319X00057/1-s2.0-S1544612319301710/main.pdf?X-Amz-Security-Token=IQoJb3JpZ2luX2VjEEQaCXVzLWVhc3QtMSJHMEUCIGi9zEwLxR3tauxwND9ErZgJx8kmUs7PIyoHKlNIE88oAiEA%2Br9FezTTlPhHvFqs3PY7Rsv2qziJNP7c6UkgnR7Yx%2FAqtAMIfRADGgwwNTkwMDM1NDY4NjUiDCycllS4%2F8eU2UtuwCqRA3xEWZgYHrSYxe%2F2cY35znl7%2BrYiaiqvES50SAUVz361RC1t8KSKOakdpkTNQqh4FMAAoHG8DYVmDfaCYFtw8KateEGvzu8HSDWA38wKQAMoaFkXFKngSaAZKXyyGCndbXEn3uU9z%2B5IeyX41AoNZU6RLGw8Cpf6lHRcfmFzinvbD4Ax76c4eEI5VPjnw4lMVBW1HRbBPOMYYc46rjYZAz%2Fh5x9svNy6r4Q5qrl8Kemr8po%2FF%2Ful51FyZPpnF%2B0V876hZ5qIVTilOrMvMZF1klNwSETEAyFHcWiNvIC4BjVZrE%2B9rJ82HddrJ9z0xzsMLnBap9FZPoF8efCUg%2BAgIZUgtnDFB9T1rm2S%2F2097Phqp2KZSZ7YIKlpXUc3v5n2wgS7v5Kg71nbhwqD3Zq2ZSWgaPKnqAWOEAsC8Ffiw%2BuEu2%2BcXUd3JIedUwkxnlfWM3T4BpAj3BgzVEjk%2Bh%2B0n6%2Frb6RR8AUoPpLXDWRcV9qxqAwj%2BrGHzzOs61KCKtAF0lYox7CcPcYZBF8wFdvtRVXpMJ3gwfUFOusBj00%2BzO8IR7VmVKT4TDWkuGirpAo73ct8sss3qua74WEFhT5KFtdXjPK4JyzI0wsz%2BAUguCHuNJTpjTqmaaa7CY3AB7B4YA3hBnuGYwwAYOwvDLoxngtORKlXYRZ9I6gctLps6wnWWKhKL5zfn5mS3csgfUJg%2FL%2Fn49%2BfEPRf35Iyq1eseCXUHo5ytNoXE0LNlN2uD4B4jRP%2BjfhuMMhtLy7PAtEDvJfWSA2pGzH89EzJSU3K9LQKog6PskJW02Nypyanl4yypIH8yk0e5vSNwik2577pAAWKtzGo59kT7PMcKxi25lho7SF8Yw%3D%3D&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20200504T203914Z&X-Amz-SignedHeaders=host&X-Amz-Expires=300&X-Amz-Credential=ASIAQ3PHCVTY2XEEG2QF%2F20200504%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=10f554079fdf249329cd9c737dc737eb554f5c9c26a766fec650d7a84ad43d61&hash=66f198c652deb4d4a09d0a7085defe74c527b2f7a7c167a06649922fe459fd0f&host=68042c943591013ac2b2430a89b270f6af2c76d8dfd086a07176afe7c76c2c61&pii=S1544612319301710&tid=spdf-f91c3ce5-7b9b-4bee-b472-c03b96ba2257&sid=c2e7b8e390a5b3418c085c652c125847f6c9gxrqb&type=client) 记录了周一**较高的正平均回报**。[马和谷崎润一郎(2019)](https://www.sciencedirect.com/science/article/pii/S0275531918307827/pdfft?casa_token=I9_0RYujoigAAAAA:TmfJrQwOCQcGiaIus8CjgT2Zp1BVNJWGpHf3er6P_ecHVP1bCtgLgxBUv_q_m0ftTtdLefaePD4&md5=c4a0839cdf0457dd5b3ccb4087f6672d&pid=1-s2.0-S0275531918307827-main.pdf) **在周一报告了同样较高的比特币平均收益率和波动性，而最低值出现在周三。****

**[Caporale 和 Plastun (2019)](https://reader.elsevier.com/reader/sd/pii/S1544612318304240?token=B6636990A9C73D58086DABA757CAC967E44F4667DDC91414DD5B6F58157C8A9DFA2DA62DED0E692697048DCE59A39F80) 调查了 4 种加密货币(比特币、莱特币、Dash 和 Ripple)的星期几效应，但**只在比特币的情况下发现了异常**。作者还报告称，与其他日子相比，比特币在周一的平均回报率更高。这些结果被 [Aharon 和 Qadan (2019)](https://pdf.sciencedirectassets.com/273054/1-s2.0-S1544612319X00057/1-s2.0-S1544612317307894/main.pdf?X-Amz-Security-Token=IQoJb3JpZ2luX2VjEEQaCXVzLWVhc3QtMSJGMEQCIE%2BDp8fqN%2FIwu5H2LqQlafJInbip7USlju4DOSXxJpcOAiBOOlT4%2FmWJd6q11Llu4%2Fz1EjO0Sd2wAbdEJd32uuWy3Sq0Awh9EAMaDDA1OTAwMzU0Njg2NSIMl3kn5spQtzFBOyQ9KpEDVeDuXH9D5Tw9sSNOjyo6D5hUPjf7iDlbGcvlCIEuM%2BzkdbeS9wymQyS1%2B6CtsPBdzZPxnpS%2FKJjuS%2BTR1TzAvfnMDL5rMUdd5Z4a6NGyBuw9jM4zAPDBHf19hefbHfwzuZjxXASp5TfRLHnnJm0KMZbxdVCaphg6MFafpG8Covh576RuaTSjkZdogJQRvnrT2D951dS7I1R6KlikfJWvVlvkcY5f1CmjiezZqnuVK5I6UwuSS7RZ%2Fd40aBMcWMSj0R9EbNjR9Rzb5VMzVU1ozWFmm8oYrCB4wwryvPJ4qQ%2B5bmwe4oiIzdvIz4R%2BxWZBT10lAIg%2FHCQQMyEB8xEdLxfxDPdtcarYIqPYFxd72JNuZ0bmA05%2BmPhjcJam143h%2B7GlWPChfqq8eK1IEe8eSfew9jWKbiUws6cuuFB80SkW4UFJBgTsoBw0ztJZ7ddo1uAShVsYSNhXr9G6ypCqJQ2bb9dAUR%2B6xZDYPRqPE5A03KdUQqm3Ef4XVL8cOz354l%2FUhR1nyadGVEiHGogMrCQw%2F%2BDB9QU67AFt6Dpyof2YwzHX8t5WQqaQC%2BKBqOQPwgGB2KeYbTNQ91RzrajgD2zPgAcSX%2FvrJCpalMujCfEql1lcoEpUoZjdwuDtRJQb%2B1%2FM%2FpzD04OJIFSaKCkYQq%2FpNxvexapJlhQzRscwECEvxxLwLVzpMgHF6oRJzFyCnytSdGd9VmgxVzensL%2BF3ey5kIrSIhXlMonwmYa9W%2Fu%2BsrklY7yU99o70Yo4JPTocqrPO9y%2BdK9lbevvv0QNxpuU%2BwN1RsBygkK%2F9hO112Ss20Dk2hjJSrENVZH5xMoEgII0Spb%2BsRwocwZQ%2B2zzmMnUh%2BlcZQ%3D%3D&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20200504T204209Z&X-Amz-SignedHeaders=host&X-Amz-Expires=300&X-Amz-Credential=ASIAQ3PHCVTYTXMCWZR4%2F20200504%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=5cb354aea3f8ce847b59c39f5abf74871c3a772d485de7888c9d81673f82cd6d&hash=63c8a702f968ca17fbad3fefa931ca4a20d5e599d4bf36d7fc94af3cc564dc24&host=68042c943591013ac2b2430a89b270f6af2c76d8dfd086a07176afe7c76c2c61&pii=S1544612317307894&tid=spdf-bceafe3f-f2a2-45d1-bbeb-205619a3837a&sid=c2e7b8e390a5b3418c085c652c125847f6c9gxrqb&type=client) 的发现**再次证实，他们也揭示了比特币在**周一**显著更高的回报和波动性。****

## **终究没有效果？**

**相比之下， [Mbanga (2019)](https://www.tandfonline.com/doi/full/10.1080/13504851.2018.1497844?casa_token=F4GvLSYqU5kAAAAA%3A7h1BZm0PE2KgAWABAGH5dgSUNs7r88ClOXn681ceF3aPh9GnHF_Wm2WSwnoIsnBn745XY2f4zedGkQ) 认为，**比特币的价格聚集并不专属于一周中的某一天。****

**[亚亚和奥格本纳(2019)](https://mpra.ub.uni-muenchen.de/91429/1/MPRA_paper_91429.pdf) 扩展了对 13 种流行加密货币的星期效应的研究。作者的结果表明 6 种加密货币的市场效率低下:Doge、以太坊、Maidsafecoin、Ripple、Stellar 和 Verge。**

# **3.数据**

**检索了 Coinmarketcap 上 Decred(股票代码:DCR)的每日价格**。可用范围周期从 2016 年 **11/02 到 2019 年**12 月 31 日，包含 1420 个独特的观测值。计算每日对数收益。****

**![](img/32b02fbdc443f1319a3ba06ef5e346e0.png)**

***Figure 1: D*aily DCR Prices between February 11, 2016 — December 31, 2019**

**图 1 显示了 DCR 价格的时间序列数据。在取样期间，DCR 的**日平均回报率为 0.2%，标准差为 7.5%**。**

# **4.方法**

****回归模型**用于建立**星期几和每日收益之间的关系，以测试星期几效应。在一年的月份和它的回报之间应用了相同的方法。****

**完整的方法在这里解释[。](https://github.com/alienalex6/dcr_dayoftheweek/blob/master/methodology)**

# **5.星期分析**

## **全样本**

**从整个周期来看，**周六的收益**是一周所有日子中最高的(1.4%)，在 10%的水平上具有统计学意义。因此，可以假设在星期六有一个**星期效应，**，即使它只在 10%的水平上是显著的。**

**最低平均回报日建议为星期二；然而，它在统计上并不显著。**

**![](img/3533d363e824f49884955f5bf9674a15.png)**

**Table 1: Daily Mean Return by Day of the Week (from regression model) for Full Sample**

**周一的平均回报在统计上并不显著，但我们发现它是负的，就像我们以前在传统市场看到的那样。因此，我不能断定 DCR 的回报存在周末效应。**

## **年度周期效应**

**当把样本分成市场上已经存在的年份时，我发现其他重要的日子具有更强的显著性。在 **2016** 期间，价格涨幅最高的日子是在**周一(3.2%)和周三(2.7%)** ，两者在 **5%水平**上具有统计显著性。在消极的一面，在 10%水平上的统计显著结果是在周日发现的(-1.6%)。**

**![](img/69b5aef724b69c9470e295a5099b4ed8.png)**

**Table 2: Daily Mean Return by Day of the Week (from regression model) in 2016**

**在 2017 年的**期间，与许多其他加密货币一样，Decred 经历了价格飙升，这可能影响了日常模式，因此我**在今年没有发现任何一天的周效应**。****

**![](img/9ed43e5f053728761cb53eec43d404a1.png)**

**Table 3: Daily Mean Return by Day of the Week (from regression model) in 2017**

**在 **2018 年**，我发现周五的日回报率为**的 2.7%，具有统计学意义(5%)。Decred 在今年也经历了价格的大幅上涨，这改变了迄今为止的日常模式。****

**![](img/53ae6e7cd3b7e255504fcf356c43d243.png)**

**Table 4: Daily Mean Return by Day of the Week (from regression model) in 2018**

**相比之下，去年周四的回报率为负 1.8%，具有统计学意义。**

**![](img/aebba85352c181e00e9420392565ebd8.png)**

**Table 5: Daily Mean Return by Day of the Week (from regression model) in 2019**

**日均回报在几年间发生了变化，这挑战了任何关于日回报的结论性模式。然而，当考虑一个更大的周期(全样本)和一个在星期六可观察到的模式时，有一个重要的结果——所有周期的正平均回报。**

# **6.一年中的月份分析**

**在这种情况下，我发现 1 月份的月平均回报率在 10%的水平上具有统计意义(1.7%)。这一发现与股票市场中广为人知的一月效应(1 月份的价格涨幅高于其他月份)是一致的。尽管这一结果仅在 10%的水平上是显著的，但它显示了这种效应也存在于加密货币市场中的一些迹象，在这种情况下，关于法令。**

**![](img/c36c23e235baa607e437775e944629db.png)**

**Table 6: Mean Return by Month of the Year (from regression model) for the full period**

# **7.投资策略模拟**

**通过了解一周中每天的平均收益，投资者可以根据当天的负/正收益制定简单的策略。在本文中，我们在没有交易成本和流动性问题的理论场景中模拟 **4 种策略**，基于表 1 中的系数(完整样本):**

1.  ****“持有”:**在使用样本的第一天(11/02/2016)买入 DCR，在样本的最后一天(31/12/2019)卖出。**
2.  ****【周末】**:基于周末效应。基于周五收盘价买入，周日收盘价卖出的策略。**
3.  ****【周六】**:如果周六表现出唯一显著的正收益，我建议以周五收盘价买入，周六收盘价卖出为主的策略。**
4.  ****【正】**:基于只在连续正回报的日子里持有货币。DCR 回报在周五和周六为正，因此投资者可以在周四的收盘价买入，并持有到周六的收盘价，利用连续的正日。**

**所有的策略都基于这样一个假设，即投资者在样本期内每周都有固定数量的投资。**

**![](img/9d99a361912381c705d42cfcbe3f4c56.png)**

***Figure 2: Investment Strategies Cumulative Returns for the full sample***

**图 2 显示了每个策略在整个样本期间的累积回报。**

**如果投资者选择从样本的第一天到最后一天持有货币，他/她有一段时间的策略是负面的，这在任何其他策略中都是看不到的。然而，“持有”策略从那个时期恢复过来，成为次佳策略。**

**最后，**“积极”策略提供了最好的累积回报**。然而，**这些累积收益并没有考虑到与 DCR 每周买卖相关的交易成本**以及**其流动性风险**。因此，如果将这些考虑在内，累计回报**将显著低于**(总累计回报的 35%-50%)。**

**![](img/6085af78c76b2186a9fb6fe280eb7920.png)**

**Table 7: Annualized Mean Returns, Annualized Standard Deviation, and Sharpe ratio for each strategy**

**表 7 中提供的夏普比率是一个风险调整后的指标，它为投资者提供了一个更好的评估，即哪种策略适合于自己的风险规避水平[(夏普，1966)](https://www.jstor.org/stable/2351741?casa_token=KO5AxwVRzA0AAAAA:JBDEGp-BhpWuLQLGIH0vahwoZd7TWdCaGjDpUHfubQ0O5K4pHxbKViBXAAO5TRvndtCIwaYQYBTZ-NiSbrLu3zl1EwlZdlRV0PJtLAMDIxYx3RF1efeu&seq=1#metadata_info_tab_contents) 。**

****“正”**策略在所有策略中提供了最佳的夏普比率(4.39)，在 1%的水平上具有统计显著性，在 5%的水平上具有统计显著性年化平均值(759%)。
正如我们所料，**“周六”**策略也为投资者提供了一个很好的选择，其形状比为 3.46，在 1%水平上具有统计显著性，年化均值为 488%，在 1%水平上具有显著性。**

**与累积回报情景一样，**如果计入交易成本，夏普比率将显著降低**——可降低 35%-50%。
本练习是基于过去回报的**，因此由于货币特征、市场条件和其他外部因素的影响，这些**并不能确定在 2020 年**将实现同样的模式或策略回报。****

# **8.外卖食品**

**观察多年来的每日模式，当分析不同时期时，我发现一周中不同日子的回报率有相当大的变化。然而，在除 2017 年之外的所有样本中，发现了一些具有统计意义的日收益率，处于 5%和 10%的水平。然而，在全部样本中—每日观察次数最多— **在周六发现了 10%水平的统计显著结果**。此外，在所有时间样本中，周六的日平均回报为正，即使在统计上不显著，这导致投资模拟的良好表现(例如，正策略和周六策略)。**

**然而，每日模式的**转变表明，没有一个**可以作为未来策略的可靠指标来考虑，即使测试策略的历史正回报也是如此。这些策略显示了采用季节性分析的可能性，但是由于没有考虑交易成本和流动性风险的**实际影响**，它们应该主要被视为理论练习。******

关于一年中的月份效应，我发现**在 Decred 的回报**中有一些一月效应的迹象，遵循与股票市场相同的行为。

值得一提的是，用于所有季节性分析的时间范围的**变化**会影响平均收益及其显著性，**因此，随着更多数据可用，结果会发生变化。**

同样，在某些分析中缺乏显著性的原因之一(例如，月份效应)可能是因为 Decred 进入市场仅 4 年，所以观察值**较少。中期而言，后续研究将是有益的。**

## 参考

完整参考列表[此处](https://github.com/alienalex6/dcr_dayoftheweek/blob/master/references)。

## 公开

> 本文不代表任何一种投资建议。它的目的是进一步教育观众有关法令的价格行为。
> 
> 如果你有兴趣投资任何加密货币或寻求专业建议，你应该开展自己的研究。

![](img/0ac758d7122ac1c2860cc155daf2c5d8.png)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)