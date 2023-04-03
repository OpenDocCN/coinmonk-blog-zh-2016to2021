# 合并 R 中的多个数据帧

> 原文：<https://medium.com/coinmonks/merging-multiple-dataframes-in-r-72629c4632a3?source=collection_archive---------1----------------------->

![](img/6be7fe15ad320a2e7369096af33b2e2b.png)

Photo by [Laurenz Kleinheider](https://unsplash.com/@laurenzpicture?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

r 有很多方便的合并和追加多个数据帧的功能。特别是，我想介绍当您希望将多个具有相同列的数据帧合并在一起时的用例。

作为一个例子，让我们[将 Kaggle](https://www.kaggle.com/NUFORC/ufo-sightings) 中的 UFO 数据集分成多个片段，看看我们如何轻松地将它们组合在一起。

为了分割数据，我们将使用 kcha 设计的以下函数:

```
ufos <- read.csv("scrubbed.csv")
splitDf <- split_k(ufos,4)
```

我们将数据分成四个数据帧，ufo1、ufo2、ufo3 和 ufo4。

```
ufo1 <- splitDf$`1`
ufo2 <- splitDf$`2`
ufo3 <- splitDf$`3`
ufo4 <- splitDf$`4`
```

现在，如果我们只想将前两个数据帧合并在一起，我们可以使用 rbind，如下所示:

```
ufo1And2 <- rbind(ufo1,ufo2)
```

我们可以使用 NROW 确认数据已经成功合并:

```
> NROW(ufo1)
[1] 20083
> NROW(ufo2)
[1] 20083
> NROW(ufo1And2)
[1] 40166
```

然而，由于我们已经将数据分成了四部分，所以不难想象我们会想要合并四个数据帧。Rbind 只接受两个数据帧，所以我们必须稍微调整代码来容纳更多的数据帧。

## **合并两个以上的数据帧**

要在 R 中合并两个以上的数据帧，我们可以将 rbind 调用与 do.call 结合起来。

```
do.call("rbind", list(dataframes to merge))
```

**工作原理:**

do.call 函数允许我们使用任何现有的 R 函数，比如前面提到的 rbind，但是在这个实例中，我们不是只传递两个数据帧，而是能够传递多个。

因此，当与 rbind 一起使用时，我们可以使用 do.call 一次合并两个以上的数据帧。

把这些放在一起，我们可以把我们的 UFO 数据帧合并到一起，如下所示:

```
ufoMerged <- do.call("rbind", list(ufo1, ufo2, ufo3, ufo4))
```

我们可以通过检查行数来确认我们的数据已成功合并:

```
> NROW(ufoMerged)
[1] 80332
> NROW(ufos)
[1] 80332
```

以及使用 dplyr 执行 anti_join，返回 null:

```
library(dplyr)
ufoChecker <- anti_join(ufoMerged, ufos)
ufoChecker
 [1] datetime             city                 state                country             
 [5] shape                duration..seconds.   duration..hours.min. comments            
 [9] date.posted          latitude             longitude           
<0 rows> (or 0-length row.names)
```

这只是合并多个数据帧的一种方法，在 [R-Bloggers](https://www.r-bloggers.com/the-rbinding-race-for-vs-do-call-vs-rbind-fill/) 你可以找到其他方法的速度比较。然而，我发现 do.call 与 rbind 结合使用是连接多个数据帧的最直观的方法之一。

## 有用的资源

 [## {do.call}功能

### allframes = sapply(1:20，function(x)read.csv(paste(x，' csv '，sep= ' . ')))> head(allframes) [，1] [，2] [，3] [，4] [，5] [，6]…

www.stat.berkeley.edu](https://www.stat.berkeley.edu/~s133/Docall.html) 

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度的加密应用](/coinmonks/buy-bitcoin-in-india-feb50ddfef94)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   杠杆代币的终极指南
*   [比特币基地评论](/coinmonks/coinbase-review-6ef4e0f56064) | [德里比特评论](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) | [FTX 评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   [Coinmetro 评论](https://coincodecap.com/coinmetro-review) | [VirgoCX 评论](https://coincodecap.com/virgocx-review)
*   [法国 4 大最佳加密副本交易平台](https://coincodecap.com/copy-trading-platforms-france)
*   [从 WazirX 切换到 CoinDCX 的 5 个理由](https://coincodecap.com/reasons-to-switch-from-wazirx-to-coindcx)
*   [联合国硬币评论](https://coincodecap.com/unocoin-review) | [最佳加密赌注硬币](https://coincodecap.com/best-crypto-staking-coins)
*   如何使用 MetaMask Wallet 获得 KCC 地址？