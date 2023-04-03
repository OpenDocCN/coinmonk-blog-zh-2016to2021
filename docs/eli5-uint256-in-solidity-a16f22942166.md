# ELI5: uint256 in Solidity

> 原文：<https://medium.com/coinmonks/eli5-uint256-in-solidity-a16f22942166?source=collection_archive---------0----------------------->

`uint256`可能是 Solidity 中使用最多的值类型。然而，如果您一直在编写像 JavaScript 或 Python 这样的高级语言，您可能并不熟悉它。在这篇文章中，我用简单的英语解释了什么是`uint256`。我将把`uint256`分成两部分(`uint`和`256`，并逐一解释。

# uint

在 Solidity 和其他语言中，比如 C#, `int`是一个有符号整数，而`uint`是一个无符号整数。

如果数字变量可以表示正数和负数，则它是有符号的:

```
-2
+2
```

然而，如果数字变量只能表示非负数(零或正数)，则它是无符号的:

```
2
2
```

因此，`uint256`意味着这个值类型只能处理非负数。

# 256

接下来，我们来思考一下`256`部分。简而言之，`256`意味着存储在该值类型中的值的大小应该小于 256 位。256 位意味着以 2 为基数的数可以存储 256 位。我来解释一下什么是基数 2 的数字，256 位数有多大。

## 基数为 2 的数字

基数为 2 的数是多少？通常，我们以十为基数(又名。decimal)，也就是说我们用十个符号(0 ~ 9)来表示每一个数。在这个计数系统中，一旦我们跳过了符号 9，我们就组合这些符号来创建新的数字:

```
0 1 2 3 4 5 6 7 8 9
10 11 12 13 14 15 16 17 18 19
```

计数系统的“基数”是用来表示世界上所有数字的符号数。我们人类主要使用十进制，可能是因为我们有十个手指。

在计算机时代，基数为 2 的数字系统(又名。二进制或二进制)是信息的基本组成部分。以 2 为基数计数时，计算机只使用两个符号(0 和 1)。一旦计算机越过符号 1，它就组合这些符号来创建新的数字:

```
0 1
10 11
```

为什么计算机要使用基数为 2 的系统？简单的回答是因为计算机中的每个数字都是电信号。当您只区分“开”状态和“关”状态时，这是可靠的，而不是以 10 为基数为每个符号设置特定的电压。

## 256 位的大小

现在，正如我前面说过的，256 位数字可以存储 256 位以 2 为基数的数字。但是这个有多大呢？为了了解它的大小，我们将把 256 位数转换成小数。

但是让我们从一个简单的例子开始。8 位数字在基数为 2 的数字中有 8 个数字。8 位二进制数中的最大值是`11111111`。

以下是`uint8`可以存储的小数位数:

```
2^7 * 1 + 2^6 * 1 + 2^5 * 1 + 2^4 * 1 + 2^3 * 1 + 2^2 * 1 + 2^1 * 1 + 2^0 * 1 
= 2^8 - 1
= 255
```

同理，`uint256`可以储存小数:

```
2^256 - 1
= 115792089237316195423570985008687907853269984665640564039457584007913129639935
```

你可以看到`uint256`是一个相当大的数字。

# 包扎

`uint256`为非负整数，最多可存储十进制的`115792089237316195423570985008687907853269984665640564039457584007913129639935`。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [Coldcard 评论](https://coincodecap.com/coldcard-review) | [BOXtradEX 评论](https://coincodecap.com/boxtradex-review)|[uni swap 指南](https://coincodecap.com/uniswap)
*   [比特币基地评论](/coinmonks/coinbase-review-6ef4e0f56064) | [德里比特评论](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) | [FTX 评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   [Coinmetro 评论](https://coincodecap.com/coinmetro-review) | [VirgoCX 评论](https://coincodecap.com/virgocx-review)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [杠杆代币](/coinmonks/leveraged-token-3f5257808b22)终极指南