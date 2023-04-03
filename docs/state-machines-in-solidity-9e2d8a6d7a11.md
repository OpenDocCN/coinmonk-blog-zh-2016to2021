# 稳固的状态机

> 原文：<https://medium.com/coinmonks/state-machines-in-solidity-9e2d8a6d7a11?source=collection_archive---------1----------------------->

本文讨论了使用状态机作为在 Solidity 中实施工作流的一种便捷方式，Solidity 是以太坊区块链的实际智能合约语言。

![](img/3ad92b7a852146c6d4c89d5f6075dd66.png)

Photo by [Franck V.](https://unsplash.com/@franckinjapan?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

状态机只处于一种状态，并通过改变输入或输入事件在状态之间移动。

在 Solidity 智能合约的情况下，消息从不同的帐户(外部帐户或…