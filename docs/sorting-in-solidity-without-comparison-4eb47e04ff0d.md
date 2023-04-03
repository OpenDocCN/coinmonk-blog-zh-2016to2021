# 无比较的可靠排序

> 原文：<https://medium.com/coinmonks/sorting-in-solidity-without-comparison-4eb47e04ff0d?source=collection_archive---------0----------------------->

## 本文讨论了在 Solidity(以太坊区块链的实际智能合约语言)中对小数字集进行排序的成本。

## 背景

在开发和测试 Datona Labs 的 Solidity Smart-Data-Access-Contract(S-DAC)模板的过程中，我们希望对一些小数字进行排序，例如:

```
function sortHands(Hand[noPlayers] memory hands) internal pure {
    for (uint player = 0…
```