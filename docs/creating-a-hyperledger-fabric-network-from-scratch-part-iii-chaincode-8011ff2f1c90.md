# 从头开始创建 Hyperledger 结构网络—第三部分链码

> 原文：<https://medium.com/coinmonks/creating-a-hyperledger-fabric-network-from-scratch-part-iii-chaincode-8011ff2f1c90?source=collection_archive---------0----------------------->

![](img/f76ec80c2b6f96a31704b65b47eaf37e.png)

Photo by [Fré Sonneveld](https://unsplash.com/@fresonneveld?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在这篇新帖中，我们将创建 Hyperledger 的“智能合同”链码，以定义我们应用程序的逻辑。为了简单起见，我们将始终使用相同的语言，链码和 SDK 都将使用 NodeJs 来编写链码。

> **更新**:在这篇文章中，我将讲述如何使用 VScode 扩展创建一个链码