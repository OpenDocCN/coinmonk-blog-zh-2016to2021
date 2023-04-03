# 用 Web3.js 监控以太坊地址

> 原文：<https://medium.com/coinmonks/monitoring-an-ethereum-address-with-web3-js-970c0a3cf96d?source=collection_archive---------0----------------------->

## 将交易实时检索到以太坊地址

![](img/d7df03af3845d961322fc50cf404508e.png)

Photo by [Arget](https://unsplash.com/@arget?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我最近在做一个新项目，突然遇到了一个问题:我必须差不多实时地将所有交易存入一个给定的账户。在浏览了 [Web3.js API 文档](https://web3js.readthedocs.io/en/v1.2.4/getting-started.html)以及堆栈溢出之后，很明显没有明确定义的方法来做到这一点，所以我尝试…