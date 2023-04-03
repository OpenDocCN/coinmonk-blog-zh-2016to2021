# 比特币的 Schnorr 签名算法和 Taproot 脚本和见证大小

> 原文：<https://medium.com/coinmonks/on-bitcoins-schnorr-signature-algorithm-and-taproot-script-and-witness-sizes-fe4d1e6591a7?source=collection_archive---------1----------------------->

![](img/2adf6ee2437981a5e7729b1f5d22fec7.png)

Photo by [André François McKenzie](https://unsplash.com/@silverhousehd?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在[最近的一篇文章](/coinmonks/on-bitcoin-transaction-sizes-97e31bc9d816)中，调查了截至 2020 年 5 月存在的所有事务输出类型的脚本和见证大小。本文是解决尚未实现但备受期待的 Pay-to-Taproot (P2TR)事务输出类型的后续文章。为此，文章首先讨论了所提出的 Schnorr 签名算法及其安全性