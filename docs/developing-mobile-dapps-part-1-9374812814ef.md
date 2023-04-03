# 开发移动 Dapps 第 1 部分

> 原文：<https://medium.com/coinmonks/developing-mobile-dapps-part-1-9374812814ef?source=collection_archive---------1----------------------->

![](img/260c1254e6a3b09745564668a1b4e93d.png)

区块链已经是一个炒作的词很长时间了，但我们经常发现实现现实世界的应用很有挑战性。对我来说，学习区块链没那么容易。我不得不浏览许多博客并交换答案来理解这些概念。别担心，在这一系列文章中，我们将学习如何用 Flutter 构建移动 Dapps。我会尽量让它简单易懂。

在我们继续实现 Dapps 之前，我们必须了解区块链和以太坊的基础知识。因此，在本文中，我们将涵盖区块链的基础知识以及与之相关的其他术语。

## 区块链

区块链简单来说就是去中心化的分布式账本。顾名思义，它只是链接在一起的块的集合，以不可能修改这些交易的方式记录交易。区块链的这一特性也被称为不变性。交易的不变性使区块链成为值得信赖的解决方案，去中心化的分布式账本带来了系统的透明性。

![](img/1ba2e61df878e74d87d3a49a24755280.png)

Collection of blocks linked together.

嘿，那太好了，但是什么是区块，区块链是如何实现不变性的？

## **什么是区块？**

为了便于理解，一个块由三部分组成，前一个块的散列、事务和当前块的散列，也称为 Merkle 树的根散列。一个块可以包含多个事务。

![](img/80f4e1233dcfd24e267ad895737c49e9.png)

Representations of blocks in blockchain.

除了这三件事，一个块还包括 nonce 值，时间戳，难度目标，但我们不要深究，因为这是一个非常广泛的话题，让我们把它留到某一天。

## 为什么不可改变？

在讨论区块链如何实现不变性之前，我们先讨论一下什么是加密哈希。

加密哈希是生成一系列字符的单向过程，通常称为哈希。散列函数接受输入数据并产生相应的散列。每次将相同的输入数据传递给函数时，它都会生成相同的散列。哈希函数的这一特性使其具有确定性**。**在区块链的背景下，它使用安全哈希算法 256 函数(又名 SHA-256)来生成哈希。

SHA-256 是一种加密哈希函数，它生成固定大小的 256 位输出，无论给定的输入数据有多长，该输出几乎都是唯一的。你可以传递整个维基百科，它仍然会返回一个唯一的 256 位哈希。

![](img/3e243c78177e53baf37d80233eefd67f.png)

32 character Hash of a long paragraph from Wikipedia

输入数据中的单个字符变化会导致生成的哈希值有显著差异。这是一种被称为雪崩效应的密码函数的理想特性。在下图中，你可以看到为单词“区块链”和“区块链”生成的哈希之间的差异。

![](img/73cbf7ec66f3242669c23624ecca267a.png)

32 character Hash for Blockchain

![](img/efb438b945fe2f731a09e7e4728ce4a6.png)

32 character Hash for blockchain

SHA-256 还有一个被称为前像阻力的特性，这意味着你可以从给定的输入数据生成一个散列，但你不能从给定的散列中找到输入数据。找到原始输入的唯一方法是使用蛮力方法。在蛮力方法中，你选择一个随机输入，散列它，然后将输出与目标散列进行比较，重复直到找到匹配。找到原始输入数据是不可行的，因为除非你是魔术师，否则你需要 2 次⁸尝试来选择正确的随机输入。

Magician GIF

现在我们已经了解了哈希的工作原理，是时候讨论区块链如何实现不变性了。前一个区块的哈希包含在新区块的哈希中，区块链的所有区块都建立在彼此之上。没有先前的散列，就没有连接。因此，由于雪崩效应，如果我们改变数据块中的任何一条信息，哈希将会改变，这将会打破这个链。

## 采矿

如果你之前听说过区块链，那你一定碰到过挖矿这个词。挖掘是一种开销很大的计算，由挖掘器执行，以生成块的特定散列并验证块中事务的合法性。矿工执行的昂贵计算被称为工作证明，这是区块链使用的一种不可信的分布式共识算法。

每个块都有一个特定的难度级别，它定义了哈希的阈值级别。每次 miner 生成一个新的 hash，都会与难度阈值进行比较。重复该过程，直到生成的散列小于阈值。

您可能想知道，既然我们在块中有相同的事务，我们如何每次都生成一个新的散列。是的，您说得对。由于加密函数的确定性，我们无法使用相同的交易列表生成新的哈希。但是如果你还记得，我们讨论过块也由 nonce 值、时间戳和难度目标组成。随机数是与块的散列相加的任意数字。如果散列不小于难度阈值，那么 nonce 被改变，并且这继续重复一百万次，直到最后，它小于给定的阈值。

好吧，那很好，但是我们为什么要改变难度门槛呢？嗯，这是个有趣的问题。因此，每当创建一个新数据块时，它就会在网络中传播，这样每个数据块在分类帐中都有一条新交易的记录。理论上，在比特币中，一个新块在网络中传播需要大约 10 分钟，在以太坊中需要 10-19 分钟。如果难度阈值较小，将容易生成散列，并且可能出现每个块都没有更新的分类帐的情况，这可能导致网络中的不一致性。因此，为了将块创建的平均时间维持在 10 分钟，在比特币中每 2016 个块之后改变难度阈值。

本文到此为止。在下一部分，我们将讨论以太坊和与之相关的概念。如果你喜欢这篇文章，别忘了欣赏一下

如果您有任何建议、反馈或疑问，欢迎在下面发表评论，或者您可以通过

[](https://twitter.com/ayushbherwani) [## 阿尤什·贝尔瓦尼

### Ayush Bherwani 的最新推文(@ayushbherwani)。主办方@fluttervadodara，负责人@DSC_PU，创始人@wiz_a_thon…

twitter.com](https://twitter.com/ayushbherwani) [](https://www.linkedin.com/in/ayushbherwani/) [## Ayush B. - Flutter 开发者实习生- AV DEVS 解决方案| LinkedIn

### Ayush 是一名 Flutter 和 Android 开发人员。他擅长 Flutter，并在初创公司实习过，建立了一个…

www.linkedin.com](https://www.linkedin.com/in/ayushbherwani/) 

查看我最近的文章

[](/analytics-vidhya/machine-learning-for-flutter-developers-db15c23e3a60) [## 颤振中的机器学习

### 人们总是听到别人谈论机器学习，但对这意味着什么只有一个模糊的概念。他们经常发现…

medium.com](/analytics-vidhya/machine-learning-for-flutter-developers-db15c23e3a60) [](/@ayushbherwani/notification-badge-in-flutter-c776a6194936) [## 飘动中的通知徽章

### 这是我第一篇关于媒介的文章，是关于颤动的。如果你正在读这篇文章，这可能意味着你一定已经…

medium.com](/@ayushbherwani/notification-badge-in-flutter-c776a6194936) [](/@ayushbherwani/flutter-credit-card-animation-ad43a15a7491) [## 颤动的信用卡动画

### 动画是伟大的 UX 设计的有益补充，并提供了一层喜悦，乐趣，保证，和意义…

medium.com](/@ayushbherwani/flutter-credit-card-animation-ad43a15a7491) 

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)[![](img/e9dbce386c4f90837b5db529a4c87766.png)](https://coincodecap.com)