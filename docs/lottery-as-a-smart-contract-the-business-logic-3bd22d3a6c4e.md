# 彩票作为一个聪明的合同:商业逻辑

> 原文：<https://medium.com/coinmonks/lottery-as-a-smart-contract-the-business-logic-3bd22d3a6c4e?source=collection_archive---------2----------------------->

![](img/bb19463f635d16aa9ab5fc5fec8c44dd.png)

Photo by [Alex Chambers](https://unsplash.com/photos/CyMd0vOYFfU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/dice?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

编写彩票合同是学习重要的智能合同概念(如状态、事件和可见性)的好方法。在这个 3 部分系列中，我将描述彩票智能合约背后的逻辑和代码。在开发我的彩票合同时，我使用了 [Oraclize](http://www.oraclize.it/) ，这是一个执行外部 JSON web 服务的数据载体服务。