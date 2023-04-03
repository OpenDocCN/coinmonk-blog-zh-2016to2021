# 智能合同 101:以太坊虚拟机(EVM)

> 原文：<https://medium.com/coinmonks/smart-contracts-101-the-ethereum-virtual-machine-cc58c12468bf?source=collection_archive---------1----------------------->

![](img/041d647ce0b793b961aef90d5006a2b1.png)

Photo by [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

**在我们开始之前** *这篇文章是从头开始介绍智能合约基本概念的系列文章的第二部分。之前的帖子可以在这里找到:* [*智能合约 101:以太坊上的智能合约介绍*](/@bobface/smart-contracts-101-an-introduction-to-smart-contracts-on-ethereum-85cc70ce1943)

# 以太坊虚拟机(EVM)

当智能合约被调用时，它的代码在一个叫做*以太坊虚拟机(EVM)* 的环境中执行。EVM 可以想象成一个运行在 256 位字上的虚拟处理器。与现代硬件处理器相比，EVM 提供的指令集相当有限。然而，它足够强大，可以实现图灵完备性。这有其自身的含义，我们将在本文中进一步探讨。

**指令和字节码** 智能合约的代码叫做 *EVM 字节码*。字节码本身由*指令*组成，也称为*操作码*。指令告诉虚拟处理器做什么。例如，执行 *ADD* 指令将导致堆栈上最上面的两个数相加。有关所有操作码的完整参考，请访问 [ethervm.io](https://ethervm.io)

一旦一个合同被部署到区块链，它的字节码将被永久保存，从而对每个人公开可见。它也是不可变的，一旦被部署，即使它的创建者也不能改变它。

*举例。*访问以太网扫描上 Dai 令牌的[合同页面，并向下滚动到*合同创建代码。你现在看到的是十六进制表示的合同字节码。您可以点击*切换到操作码视图*以人类可读的形式查看字节码。*](https://etherscan.io/address/0x6b175474e89094c44da98b954eedeac495271d0f#code)

# 智能合约的属性

智能合同状态由多个不同的属性定义:

*   *由创建者地址和[账号随机数](https://ethereum.stackexchange.com/questions/27432/what-is-nonce-in-ethereum-how-does-it-prevent-double-spending)推导出的地址*。还有 *CREATE2* 指令，其结果地址的计算是不同的。参见[使用以太坊的 CREATE2 on Hackernoon](https://hackernoon.com/using-ethereums-create2-nw2137q7) 。
*   *乙醚中的一种平衡*。契约能够发送和接收以太。
*   *当契约被调用时，从第一条指令开始执行的字节码*。
*   *持久存储*在合同执行期间保存。存储可以理解为合同的硬盘。

此外，在执行期间，有更多的地址空间和属性可用*:*

*   *内存*可以想象成 [RAM](https://en.wikipedia.org/wiki/Random-access_memory) 。
*   *一个堆栈*类似于正常流程中的堆栈。参见[堆栈](https://www.bottomupcs.com/elements_of_a_process.xhtml)。
*   *全局变量*如当前时间戳或当前块号。
*   *Calldata* 可以提供给合同执行的附加数据，如参数。

EVM 提供了与上面列出的所有内容进行交互的指令。

顺便提一下:EVM 提供的地址空间是严格分开的。这种分离的副作用是它阻止了一些经典的软件开发模式，比如重写返回指针和将代码执行重定向到堆栈。然而，这并不意味着智能合同不会被利用！我们将在本系列的后面讨论合同安全性。

# 图灵完备性和执行成本

图灵完备性是一个复杂的话题，需要进一步的理论概念的知识来理解。正式的定义是这样的:

> 能够计算每一个图灵- [可计算函数](https://en.wikipedia.org/wiki/Computable_function)的计算系统称为图灵完备(或图灵强大)。或者，这样的系统可以模拟通用图灵机。[来源](https://en.wikipedia.org/wiki/Turing_completeness)

幸运的是，我们不需要理解完整的形式定义。对于我们的用例来说，一个普遍接受的简化是足够的:

> 图灵完全意味着你的机制可以运行你能想到的任何算法，不管它有多复杂、多深奥、多递归、多复杂、多长……,也不管评估它需要多少存储空间或时间。[来源](https://cs.stackexchange.com/questions/71473/what-does-being-turing-complete-mean)

**问题** 关于图灵完全机器(在这种情况下机器是我们的 EVM)的一个已被证明的事实是，不可能有一个程序，当给定任何程序任何输入时，它总是能够可靠地确定执行是将结束还是将永远继续。这个问题被称为[停顿问题](https://en.wikipedia.org/wiki/Halting_problem)。

随着 EVM 的完成，是什么阻止了恶意参与者部署智能合同，该合同将陷入无限循环，导致全球以太坊节点从其主机上吃掉越来越多的资源，直到被操作系统杀死并离线——实际上从内部对以太坊网络造成了全球性的 [DOS 攻击](https://en.wikipedia.org/wiki/Denial-of-service_attack)?

即使我们忽略无限循环，仍然有可能通过执行复杂的指令(如计算输入的 SHA3 哈希的 *SHA3* 指令)给节点操作处理器带来大量负载，导致节点消耗更多的资源和功率。我们如何在不限制 EVM 功能的情况下，确保节点运营商因其提供的资源而获得补偿？

**解决方案** EVM 提供的每一条指令都有一个附加的执行成本——燃气成本*。Gas 是一个虚拟单位，用于衡量合同执行复杂性和资源使用情况，在任何地方都不能买卖。*

*当契约运行时，节点跟踪它消耗的气体。如果下一条指令将导致消耗的气体量超过*气体限制*——由导致合同执行的交易的发起者提供的值——它将中止运行并将整个交易标记为失败。*

*交易的发起者还提供另一个称为*天然气价格*的值，该值是发起者愿意支付的单位消耗天然气的乙醚量。该值通常用 *Gwei* 测量，其中 *1 Gwei = 10^(-9)乙醚= 0.000000001 乙醚*。*

*最后，交易费用可以计算为*消耗单位天然气的数量*天然气价格*。该费用从发起交易的账户支付给开采包含该交易的区块的开采者。*

# *摘要*

*在这一部分中，我们介绍了 EVM 的基本内部工作原理，这将为我们接下来的部分打下基础。在下一部分中，我们将探索以太坊网络中不同种类的节点以及它们所扮演的角色。*

**我很高兴收到您对以下帖子内容的反馈和建议。随时联系我！**

## ***还有，读***

*   *[顶级 DeFi 项目](/coinmonks/defi-future-10-promising-projects-in-the-defi-world-ff2b697ab006)*
*   *[unis WAP 最佳钱包](/coinmonks/best-wallets-to-use-uniswap-e91a6385d9e8)*
*   *[面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)*

> *[直接在您的收件箱中获得最佳软件交易](https://coincodecap.com?utm_source=coinmonks)*

*[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)*