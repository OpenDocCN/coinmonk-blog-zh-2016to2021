# 稠度函数的气体成本

> 原文：<https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678?source=collection_archive---------0----------------------->

本文讨论了在 Solidity——以太坊区块链实际上的智能契约语言——中使用契约和库函数的成本(有时令人吃惊)。

![](img/a72724d1639f0083799cc46b1ac23194.png)

Photo by [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 背景

在开发 Datona Labs 的身份契约模板的过程中，我们希望提供有用的错误消息，这需要字符串操作，如连接，例如: