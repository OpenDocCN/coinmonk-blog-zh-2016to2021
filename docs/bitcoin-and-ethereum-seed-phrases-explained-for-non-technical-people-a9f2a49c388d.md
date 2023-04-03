# 为非技术人员解释的比特币和以太坊种子短语

> 原文：<https://medium.com/coinmonks/bitcoin-and-ethereum-seed-phrases-explained-for-non-technical-people-a9f2a49c388d?source=collection_archive---------0----------------------->

![](img/7893084630b489656126c58f6bdcf2cb.png)

## 没有可怕的加密技术

说区块链工业正以惊人的速度变化是多余的，这意味着我倾向于及时(贾斯汀时间？)对大部分技术性的东西学习哲学。我目前的主要热情是以太坊开发，所以我只知道我需要的关于密码学的基本原理。一旦我准备好在主网上发布一些真实的东西，我会把我的大脑完全放在加密安全上。然而，在那之前，有些事情已经变得司空见惯，再也无法回避或推迟了。对于那些使用 metamask 和其他 HD 钱包的人来说，您可能已经熟悉了私钥生成的种子短语/种子助记符/通行短语/备份短语结构。在早期，创造一个新钱包意味着写下一长串数字和字母。然后，几乎是突然间，一切都变了，世界开始使用一个由 12 个常见英语单词组成的短语，看起来像这样:

```
dove lumber quote board young robust kit invite plastic regular skull history
```

那么，私钥和公钥去了哪里？有没有可能在不陷入可怕的数学和计算机科学的情况下，对事情为什么会这样有一个广泛的理解？答案是肯定的。我在狂怒的建筑中稍作停顿，迅速吐出这篇文章，因为让如此广泛使用的东西只有艾伦·都灵(Alan Turin)和维塔利克·布特林(Vitalik Buterin)这样的人才能在智力上理解，这似乎是不公平的。

*TL；DR 这篇文章已经是 tl 了；BIP39 的 dr。抱歉。*

**你应该熟悉的术语:算法(** [**)参见这里的一个冗长的例子，什么算法是**](/@justingoro/using-algorithms-to-explain-socialism-and-capitalism-9739cda6f08e) **、以太坊、** [**、比特币**](https://blog.coincodecap.com/a-candid-explanation-of-bitcoin) **和公钥-私钥加密。**

***免责声明:这并不完整和完美。足以给你一个直观的理解。还有一些细节我不是 100%确定，我就说这么多。欢迎修改，但请作为私人评论提交，否则如果它们不再适用，对未来的读者就没有意义了。***

> 还念:最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)

## 快速预封顶

如果你仍然不清楚什么是私钥-公钥对，可以打个比方:私钥就是你的 gmail 密码。公钥是你的电子邮件地址。每个人都可以看到你的电子邮件地址，他们需要它来发送邮件给你。当您发送邮件时，他们会看到邮件是从您的电子邮件地址发出的。然而，只有你知道你的 gmail 密码。这给了你发送邮件和阅读邮件的权力。电子邮件地址和密码成对存在，就像这个(justin@fakeland.com；123456)，其中每个电子邮件地址只能有一个对应的密码，反之亦然。为什么称之为一对？我注意到，越是倾向于数学的人，在将事物分类成组、套、对、环等方面就越兴奋。我不知道为什么，但这似乎让他们很开心。因此，每当你听到技术人员使用术语“公钥-私钥对”时，想想“电子邮件地址及其相应的密码。”

## BIP39

比特币有比特币改进提案(BIP ),在经历漫长的接受或拒绝过程之前，公众可以提交该提案进行审查。在以太坊中，这种对等物通常被称为以太坊征求意见稿(ERC)。一个众所周知的例子，ERC20 是令牌交换的建议标准，当然是第 20 代 ERC。大多数 BIP 和 ERC 都失败了，但是 [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) 确实成功了，并解释了 ***种子短语助记法如何工作*** 。以太坊社区有借鉴比特币的好主意的历史，因此这一标准现在适用于两者。

因此，让我们深入了解 BIP39 的工作原理:

你从一本 2048 个单词的字典开始。每个单词的前 4 个字母必须是唯一的。也就是说，字典中没有其他单词可以以这四个字母开头。这允许您存储一个种子短语，如下所示

性感的 doge 人熊猪屋

如同

性感的男人之家

字典的排序和索引方式如下

1 |算盘

2 |雪花石膏

3 |阿尔巴尼亚

…

2048 |动物学

按照惯例，全世界使用同一本字典。这只是让钱包之间的兼容性更容易维护，但如果你想对你的安全完全 Satoshi，你可以制作自己独特的列表，如果你喜欢，编写迎合该列表的钱包。

下一步是要有一个随机数发生器，它能产生 1 到 2048 之间的数字。这在计算机科学中是相当微不足道的，天才们选择了一个可靠的随机和众所周知的算法来完成这项工作。

所以现在你用字典生成 12 个数字，对应你的 12 个单词。

## BIP32

在 BIP39 之前，BIP32 建立了一个新的公私密钥生成标准，称为分级确定性(HD)钱包。其背后的概念远没有选择这个词那么令人生畏。HD 的神奇之处在于，您可以生成一个主私钥，您可以通过一种算法来生成另一个唯一的私钥。然后，您可以使用第二个私钥来生成更多的私钥。每个私钥都可以通过(又一个)算法来产生其对应的公钥。我不确定一把万能钥匙能产生多少对这样连续的密钥对，但我认为每把万能钥匙能产生大约 20 亿个。

## 把所有的放在一起

你写下的助记短语可以使用字典反向转换成 1 到 2048 之间的数字列表。这些词只是为了让人们更容易理解并通过电话传达(对于那些不相信 NSA 存在的人来说)。所以我们的列表

```
**dove lumber quote board young robust kit invite plastic regular skull history**will be converted to something like**101 900 1781 62 568 123 88 1322 1099 1544 666** finally you just bunch that all together as one long string to form a unique string of numbers:**10190017816256812388132210991544666**
```

使用一种特殊的算法，你可以将最终的字符串转换成用于高清钱包的主私钥。然后，您可以根据需要生成一个庞大的新的私有-公共密钥对列表。你可能只需要 1 个，但是 Satoshi 建议每笔交易都使用一个新的密钥对。这个标准使得这种方式比以往任何时候都更容易。你所需要的是 12 个单词，这是你打开(几乎)无尽钥匙对的万能钥匙。

就我个人而言，我使用一个关键短语来生成 10 个唯一的地址，我加载了假以太网，并用于在本地测试我的智能合约，但我认为商家和交易所充分利用了这种密钥存储和生成系统的灵活性和易用性。

## **同样，阅读**

*   [最好的加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   [unis WAP 最佳钱包](/coinmonks/best-wallets-to-use-uniswap-e91a6385d9e8)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [bits gap review](https://blog.coincodecap.com/bitsgap-review)——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [3commas Review](https://blog.coincodecap.com/3commas-review-an-excellent-crypto-trading-bot) |一款优秀的密码交易机器人
*   [3Commas vs Cryptohopper](/coinmonks/cryptohopper-vs-3commas-vs-shrimpy-a2c16095b8fe)
*   Bitmex 上的[保证金交易的白痴指南](/coinmonks/the-idiots-guide-to-margin-trading-on-bitmex-dbbd7742c6fc?source=friends_link&sk=7bfa99d2a181142510c8442c8ddb0786)
*   加密摇摆交易的权威指南
*   [Bitmex 高级保证金交易指南](/coinmonks/bitmex-advanced-margin-trading-guide-2270c195ce25?source=friends_link&sk=1d986cca731f5084b9a2db4a4bc4a7ad)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com?utm_source=coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)