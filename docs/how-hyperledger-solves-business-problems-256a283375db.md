# Hyperledger 如何解决业务问题

> 原文：<https://medium.com/coinmonks/how-hyperledger-solves-business-problems-256a283375db?source=collection_archive---------2----------------------->

在本文中，我将分享如何在现实生活中应用 Hyperledger Fabric 的属性。

让我们从非常简单的场景开始。假设我们有两家公司或两方——A 和 B，它们之间有一些业务流程。他们进行大量交易&交换大量信息——文档、数字资产、货币、代币等。

![](img/e29ecda64bda9a36600d8f1918811112.png)

那么共享信息存储在哪里呢？现在，考虑到双方都不信任对方，通常情况下，他们将所有记录与软件一起存储在服务器中，乙方也是如此。但是，由于他们必须一起进行业务交易，在某个时间点，这两家公司停止并开始匹配记录。记录相互吻合的概率通常很小，但是如果它们匹配，这个过程就会继续。但在现实中，这种情况从未发生过&总会有一些不匹配。因此，在这一点上，公司开始了繁琐而漫长的结算过程，在某些情况下可能需要几天到几周到几个月甚至几年的时间。更糟糕的是，和解过程中最糟糕的部分是双方都不支付对方，因为双方都不知道要支付多少钱。所以这个过程有严重的缺陷。虽然有一些技术可以优化它，但它们仍然没有多大的规模。

现在想象一下，你必须管理一个供应链，其中有 10 方以一种非常复杂的方式相互沟通。对于上面的场景，很明显，它变得乏味、具有挑战性并且几乎不可能管理。那么，这些公司都做些什么呢？他们雇佣第三方，并通过它来引导他们所有的交易。

![](img/07f5d9ae278cd69a6d541f0fbd0a03e5.png)

第三方或验证机构负责从数据完整性到交易安全性的几乎所有事情。因此，尽管所有各方都开始与这个核查机构沟通，试图解决他们的许多问题，但这种方法总是会带来更多的问题。

1.首先，这要花很多钱。

2.第二，所有各方必须确保其他所有人也信任第三方。这种挑战越来越多，特别是如果一些当事方是国际性的。

3.这种方法的第三个也是最关键的问题是，验证方在一定程度上也依赖于人来管理他们的客户交易。这增加了人为错误、误解、不当数据处理和欺诈的可能性。

想象一下，如果有人以某种方式更改了该验证机构中的一些数据，会发生什么情况。任何一方可能都不容易意识到信息可能已经改变，这进一步升级为额外的问题。即使有最好的软件和多重制衡，现实是这些事情确实会发生，而且没有防弹方法来防止它们。验证机构或一方可以通过使用专门的硬件访问控制、安全软件和个人背景检查来应用数千种程序措施，但是这仍然是不可靠的。

虽然传统上，我们在业务中一直遵循类似的方法，但我们现在必须意识到存在差距&因此可能不是理想的方法。现在是我们彻底转向“智能合同”的时候了。

![](img/aa0d22546c2a5b36d8bef6ffc7f29e62.png)

让我们想象一个类似的场景，就像我们之前讨论的那样，但是有一个新的视角。我们有 3 方一起工作，他们都在他们的服务器上设置了 Hyperledger。双方就特定的业务流程达成一致，并实际创建智能合同来执行达成一致的业务流程。他们的每个超级账户都和其他党派有联系。因此，他们都可以访问由智能合同强制执行的相同信息。

比方说，如果有人改变了他的超级账本中的数据，所有其他人几乎会立即意识到这一变化。请记住，此信息不是基于任何假设，有一种确定性的方法来确定这一点。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)

让我们假设一些真正优秀的黑客能够以某种方式同时更新各方之间共享的所有数据。这也不会导致问题，因为数据将无法通过加密验证。任何一方，无论是内部的还是外部的，都不可能窃取和修改任何数据。所有公司共享相同的数据，这些数据会实时自动同步。当流程决定更新分类帐时，它必须通过智能合约。在 Hyperledger 的上下文中，智能联系人被称为“链码”。

Chaincode 是一个运行在区块链之上的程序，它实现了任何应用程序如何与分类帐交互的业务规则。当提出交易时触发，它决定要应用于分类帐的状态更改。链代码中的业务规则可以允许或拒绝一个事务或操作。业务规则可以很简单，如“在转账之前，检查转账人是否有该金额”，也可以很复杂，如设置规则以允许或禁止用户参与任何活动或特定组织的任何操作，创建从属规则，如“如果满足特定标准，允许组织接受付款”。业务规则可以非常有创意，因为智能合同是使用 Google Go 或 Node.js 或 Java 编写的，可以使用任何外部库、数据结构或算法。

现在需要的是停下来思考一下 Hyperledger 如何改变多方之间的交易方式，重点是在根本不信任对方的各方之间加强信任。