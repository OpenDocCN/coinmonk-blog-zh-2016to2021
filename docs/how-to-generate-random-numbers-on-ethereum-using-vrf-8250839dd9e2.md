# 如何使用 VRF 在以太坊上生成随机数

> 原文：<https://medium.com/coinmonks/how-to-generate-random-numbers-on-ethereum-using-vrf-8250839dd9e2?source=collection_archive---------0----------------------->

## Chainlink 如何解决以太坊的“随机问题”

![](img/1190f05dc29db372e4869023ab20b10c.png)

Photo by [Black ice](https://www.pexels.com/@black-ice-551383?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) from [Pexels](https://www.pexels.com/photo/lots-of-numbers-1314543/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

**随机数和区块链*一直*不和。直到现在，区块链上还从未存在过可验证的随机函数。**

这个问题源于这样一个事实，即当事务被挖掘时，它们需要被一个以上的节点确认…