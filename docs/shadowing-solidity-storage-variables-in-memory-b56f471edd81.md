# 在内存中隐藏可靠性存储变量

> 原文：<https://medium.com/coinmonks/shadowing-solidity-storage-variables-in-memory-b56f471edd81?source=collection_archive---------2----------------------->

本文讨论了使用Solidity 中的`**memory**` 隐藏 Solidity `**storage**`变量的技术，Solidity 是以太坊区块链的实际智能合约语言。

![](img/7a6ed1323da6a31fa3062ff541a54be7.png)

Sort those jelly beans, quickly. Photo by [Patrick Fore](https://unsplash.com/@patrickian4?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) Retouched by Kitty West

# 背景

在开发和测试 Datona Labs 的 Solidity Smart-Data-Access-Contract(S-DAC)模板的过程中，我们需要对一些小型的持久性数据集进行排序，例如: