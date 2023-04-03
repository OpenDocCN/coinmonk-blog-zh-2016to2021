# 用 PyTezos 看看你投的是什么

> 原文：<https://medium.com/coinmonks/check-what-you-are-voting-for-with-pytezos-6259ccd8869e?source=collection_archive---------2----------------------->

## DYOR 系列被烤坏了

这里介绍的教程是游牧实验室在他们的帖子中展示的内容的扩展版本。主要的区别是，我尽可能地保持去中心化，只使用公共节点 API 和 [PyTezos](https://github.com/baking-bad/pytezos) 。

# 介绍

这几天对 Tezos 来说是一个火热的时刻，协议的第一次更新正在紧锣密鼓地进行，投票机制正在调试。我们目前正处于第一阶段，协议变更提案可以在网上提交，面包师可以投票赞成。推荐阅读[《修正 Tezos》](/tezos/amending-tezos-b77949d97e1e)详细了解其工作原理。

![](img/8395189e4bb1a1691a8129df07f9cfe4.png)

“Athens A” and “Athens B”

协议更新对开发者和面包师来说都是一个严峻的挑战，尤其是考虑到 Tezos 技术栈。前者应该尽可能清楚地解释提议的变化，后者应该进行自己的研究并做出明智的决定。

谈到目前的提案，游牧实验室做了非常艰苦的工作[评论他们做出的每一项承诺](https://blog.nomadic-labs.com/athens-our-proposals-for-the-first-voted-amendment.html)，这无疑值得尊重。我也和几个面包师谈过，我很高兴地了解到他们非常认真地对待投票，做所有的检查并审查所有的代码更改。

本指南的目的是深入研究修正机制的技术实现，并展示如何在研究中使用 [PyTezos 库](https://github.com/baking-bad/pytezos)。我们会尽量偏执，避免使用第三方信息源和工具。

我们开始吧！

# 笔记本

## 本地运行

你需要 python 3.6+和 Jupyter 包才能继续。

```
$ git clone [https://github.com/baking-bad/pytezos.git](https://github.com/baking-bad/pytezos.git)
$ cd pytezos
$ jupyter notebook
```

导航到“examples”文件夹并运行`dyor_voting.ipynb`。

## 在 Google Colab 上玩

也可以在线摆弄代码:[https://colab . research . Google . com/drive/1 pnaf 8 xnu 9 gtsugrpvy 2v 9 abratzitkpt](https://colab.research.google.com/drive/1pnAf8xnU9GTSUGRPVy2V9ABRAtZitkpt)

或者查看嵌入式版本:

# 摘要

几乎只使用公共 Tezos 节点和 PyTezos，我们就能做以下事情:

*   获取当前投票周期的基本信息；
*   找到求婚注射的瞬间；
*   加载提案源，用 json 和二进制格式编码；
*   计算建议 id(哈希)；
*   加载当前协议源，使用 github 类标记使代码不同并可视化；
*   查找给定提案的所有投票操作。

由此产生的不同观点:[“雅典 A”vs“003 _ psddfki 3](https://baking-bad.github.io/tezos-dyor/athens_a_vs_003_PsddFKi3.html)”、[“雅典 A”vs“雅典 B”](https://baking-bad.github.io/tezos-dyor/athens_a_vs_athens_b.html)

# 进一步的工作

## 在链上存储建议来源

如上所述，在投票期间，没有直接的方法从区块链获得礼宾来源。作为一种改进和概念验证，我将尝试在 PyTezos 中结合使用智能契约和修改后的 big_map_get 函数来实现这个特性。

## 操作搜索

展示了如何使用改进的二分搜索法算法搜索所有投票操作。在接下来的系列文章中，我将展示如何使用并行化来加速搜索，以及如何将该算法扩展到更一般的情况。

*最初发表于 2019 年 3 月 12 日 https://baking-bad.org**的* [*，在那里你可以找到文章的完整版本。*](https://baking-bad.org/blog/2019/03/12/tezos-governance-check-what-you-are-voting-for-with-pytezos-library/)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)