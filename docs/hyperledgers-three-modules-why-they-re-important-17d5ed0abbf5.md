# Hyperledger 的三个模块&它们为什么重要

> 原文：<https://medium.com/coinmonks/hyperledgers-three-modules-why-they-re-important-17d5ed0abbf5?source=collection_archive---------1----------------------->

![](img/24235445ac5e6b3297b8496f75cb5e58.png)

Source: [https://www.bluejeanmama.com/20-must-have-tools-for-emergencies/old-carpenter-tools-jpg/](https://www.bluejeanmama.com/20-must-have-tools-for-emergencies/old-carpenter-tools-jpg/)

除了 Hyperledger 已经为用户创建的一些框架之外，Hyperledger 还创建了三个当前支持 Fabric 框架的模块。

首先，什么是模块？根据 Techopedia 的定义，模块是“包含一个或多个例程的软件组件或程序的一部分。”该定义继续解释了模块的重要性，因为它们“通过允许程序员只关注软件应用程序功能的一个方面，使程序员的工作变得容易”，因为另一个方面，即建立例程的代码块，已经完成。

换句话说，如果你正在建造一所房子，你不需要同时建造建造房子的工具。你可以购买或借用你需要的锤子、管道和电缆，用它们来完成日常工作，建造你独一无二的房子。同样，在编码中，当创建一个新的应用程序时，你可以使用已有的模块——你的锤子、管道和电缆——来帮助你构建应用程序和编写程序。

为此，Hyperledger 打造了大提琴、探索者、作曲三大模块。正如 Hyperledger edX 课程所指出的，“Hyperledger 模块……是辅助软件，用于部署和维护区块链、检查分类帐中的数据，以及设计、原型制作和扩展区块链网络的工具。”这是一个很好的、广泛的概述。

现在让我们从大提琴开始，仔细看看这些模块。

## *什么是大提琴模块？*

如果你是一个小企业或者是精益生产的 T4，你可能没有足够的资源去雇佣一个区块链的开发者。这就是大提琴的用武之地。最初由 IBM 出资，赞助商有 [Soramitsu](http://soramitsu.co.jp/en/) 、[华为](http://www.huawei.com/en/?ic_medium=direct&ic_source=surlent)和[英特尔](https://www.intel.com/content/www/us/en/homepage.html)，Cello 提供区块链即服务。这意味着用户不必费力创建、管理和终止区块链。Cello 提供这些服务。

此外，Cello 还为用户提供了一个仪表盘，用户可以在区块链中搜索数据、创建区块链并对其进行管理。更进一步，如果你认为你的企业需要一组分布式分类帐网络，Cello 也可以通过它的仪表板满足这种需求。这些区块链保存在哪里？在一个由 Cello 指定的云中，它很可能指的是 IBM。

在我写这篇文章的时候，Cello 还没有上线:它还在酝酿中。

## *什么是探索者模块？*

[Hyperledger Explorer](https://www.hyperledger.org/projects/explorer) 是一款“可视化区块链运营”的工具换句话说,“探索者”为区块链、其上的信息以及区块链的工作方式提供了一个界面。如果没有像 Explorer 这样的工具，很难查看每个块、查询块上的信息或查看有关区块链的其他关键数据。这是一个区块链仪表板。

等等，你刚才不是说大提琴提供了一个区块链仪表板吗？接得好。我做到了:Cello 使用了一个 [Makefile](https://www.tutorialspoint.com/makefile/index.htm) 来利用 Explorer dashboard。所以你在 Cello 里看到的仪表盘来自于 Explorer。

引用 Hyperledger 人员的话来说，资源管理器“旨在…查看、调用、部署或查询:

*   街区；
*   交易和相关数据；
*   网络信息(名称、状态、节点列表)；
*   智能合约(链码和交易族)；
*   其他相关信息存储在台账中。"

Hyperledger Explorer 包括一些关键组件，如 [web 服务器](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_is_a_web_server)、web 用户界面(UI)[web 套接字](/@dominik.t/what-are-web-sockets-what-about-rest-apis-b9c15fd72aac)、数据库、安全存储库和区块链实现。

有没有 Explorer 这样的工具之间的区别就像是看一个盒子的分数或玩家的扑克牌和看玩家亲自玩游戏。有了方块分数或扑克牌，你可以清楚地看到玩家在那一天或那一年做了什么，这就是浏览器仪表盘可以告诉你的区块链上有什么。有一次看到一个球员打得很好，心想:“好家伙，那家伙也能打！”就像有选择地记住你的区块链上有很多好东西，但是不能清楚地记住所有的东西。

更进一步，Explorer 不仅可以帮助您根据数据得出更好的结论和做出更好的决策，而且现在团队中的每个人都可以访问同一张卡片(即相同的数据),因为他们都可以在各自的计算机上通过 Explorer 访问数据。

由 [DTCC](http://www.dtcc.com/) 、[英特尔](https://www.intel.com/content/www/us/en/homepage.html)和 [IBM](https://www.ibm.com/us-en/) 供稿，在撰写本文时，探索者尚未上线:它仍在酝酿中。

## *什么是作曲模块？*

如果现成的 Cello(上面讨论的区块链即服务模块)对你来说太现成，并且编写自己的区块链太困难或太昂贵，那么 Composer 模块可能是一条出路。 [Hyperledger Composer](https://www.hyperledger.org/projects/composer) 提供了一套工具:

*   建立你的商业区块链网络模型；
*   生成 REST APIs，用于与您的区块链网络进行交互；
*   生成骨架角度应用程序。

Hyperledger Composer 内置于 Javascript 中，它“创建了一种建模语言，允许您使用业务词汇定义构成业务网络的资产、参与者和交易。”目前，人们只能通过 Composer 而不是 Cello 来建立一个商业区块链网络，但与 Composer for Cello 的集成正在考虑中，如果不是在开发中的话。

根据 edX 课程，Hyperledger Composer 的好处是:

*   **更快地创建区块链应用**，消除从头构建区块链应用所需的大量工作；
*   **通过经过充分测试的高效设计降低风险**，整合业务和技术分析师的理解；和
*   **更大的灵活性**因为更高层次的抽象使得迭代变得更加简单。

[Oxchains，](https://www.oxchains.com/index-en.html)一家专注于验证区块链智能合约的中国公司， [IBM](https://www.ibm.com/us-en/) 为 Composer 项目做出了贡献。

在写这篇文章的时候，作曲家还在酝酿中。

## *总账模块的总体情况*

这里要注意的重要一点是，不仅所有三个模块仍在酝酿中，而且在多个框架中，他们只希望与 Hyperledger Fabric 框架一起工作。将来，这三个模块可能会与多个或所有框架一起工作。

值得重复的是，不仅仅是区块链技术，Hyperledger 区块链技术也是如此。希望随着时间的推移，两者都将继续迭代，变得更有效，并解决更多用户的问题，因为它知道什么可行，什么不可行。

## 关注更多

更多关于商业区块链 Hyperledger edX 课程介绍、《Hyperledger 区块链》和《区块链》的帖子，请关注我以获取最新消息。