# 以太坊中的时间概念

> 原文：<https://medium.com/coinmonks/the-concept-of-time-in-ethereum-f26630a616cd?source=collection_archive---------3----------------------->

![](img/9a557de9cc3daff3b1003e09907ea413.png)

Image by [Markus Kammermann](https://pixabay.com/users/makabera-6773476/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=6664622) from [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=6664622)

T4:虽然计算机上的时钟每毫秒至少滴答一次，但区块链上的时钟只在块被添加到链中时才滴答一次。对于新的 Solidity 开发者来说，区块链的时间概念可能会令人困惑。他们比较时间的方式可能是不正确的，甚至会导致抢先攻击，所以在这篇文章中，我想就这个问题阐明一些观点，并试图澄清事实。

大多数编程语言允许程序员检查和使用时间，通常由运行程序的计算机提供。例如，以下 python 脚本在运行时显示当前时间。

这是程序输出的样子。

尽管变化很小，但随着程序的执行，时间会发生变化。另一方面，如果我们看看坚固性等效代码:

该函数发出的所有事件的时间属性都是相同的，因为它是由`block.timestamp.`设置的

坚实度的`block.timestamp`是一种特殊的变量和函数；它们总是出现在全局名称空间中，主要用于提供区块链信息。`block.timestamp`以 unit256 数字的形式返回自 UNIX 纪元以来的当前块时间戳(秒)。

因此，`block.timestamp`属性对于块中的每个事务都是相同的。由于区块链的低分辨率时钟，我们永远无法期待精确的秒；因此，我们的时间比较应该总是包括大于或小于，而不是等于。

还值得记住的是，块创建者可以影响块创建的时间和处理事务的顺序，从而导致前端运行攻击，这是一个已知的以太坊协议问题。然而，这只有在块创建者成功地制造块的情况下才有可能，这是极其困难的。因此，虽然这些问题不是智能合同的实质性弱点，但在考虑设计和激励时，它们是解决的关键。

最后，谨慎的做法是，不要根据区块链提供的时间做出不重要的决定。当比较时间而不是精确的秒时，使用大于或小于。

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

medium.com](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b)