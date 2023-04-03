# 可靠性中的值数组

> 原文：<https://medium.com/coinmonks/value-arrays-in-solidity-32ca65135d5b?source=collection_archive---------1----------------------->

本文讨论了在 Solidity 中使用值数组作为减少气体消耗的一种方式，Solidity 是以太坊区块链的实际智能合约语言。

![](img/beddbe4f9a182c73de27c8cf89b75eb0.png)

Photo by [Pierre Bamin](https://unsplash.com/@bamin?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 背景

在开发和测试 Datona Labs 的 Solidity Smart-Data-Access-Contract(S-DAC)模板的过程中，我们经常需要使用小值的小数组。在本文的示例中，我研究了使用值是否…