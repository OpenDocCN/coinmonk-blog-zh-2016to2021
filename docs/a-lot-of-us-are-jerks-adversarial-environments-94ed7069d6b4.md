# 我们很多人都是混蛋:敌对的环境

> 原文：<https://medium.com/coinmonks/a-lot-of-us-are-jerks-adversarial-environments-94ed7069d6b4?source=collection_archive---------3----------------------->

## 探索拜占庭将军问题

花足够的时间在区块链空间，你会开始了解人们如何讨论[攻击](http://resources.infosecinstitute.com/blockchain-networks-possible-attacks-ways-protection/#gref)、[攻击面](https://en.wikipedia.org/wiki/Attack_surface)、[弱点](https://en.bitcoin.it/wiki/Weaknesses)、漏洞以及有人可能会欺骗你的一般假设(参见维塔利克的[信任问题](https://blog.ethereum.org/2015/04/27/visions-part-2-the-problem-of-trust/))。

这有别于[一般安全问题](https://en.wikipedia.org/wiki/Facebook%E2%80%93Cambridge_Analytica_data_scandal)(数据隐私泄露也简单讨论过[这里](/@PaulApivat/blockchains-role-in-re-decentralizing-the-web-fca85373c47d/#ac67))，我们都有过，前加密。我还会将这与我们在 ICO 领域看到的一些彻头彻尾的[骗局](https://blockgeeks.com/how-not-to-be-scammed-by-a-initial-coin-offering/)区分开来(不幸的是，这是一个[不断增长的列表](http://www.coinscamlist.com/))。

这里引用的是在 cryptoland 的一个[敌对环境](https://thecontrol.co/cryptoeconomics-101-e5c883e9a8ff/#aa66)的一般假设，这是公共区块链的背景。

## 这些假设从何而来？

事实证明，这是计算机科学中的一个基本问题；特别是在分布式系统中。a16z 的经典著作《拜占庭将军问题》(Lamport，Shostak & Pease，1982) 中的一篇论文是理解这些假设的基础。

拜占庭将军问题(BGP)基本上是一个可靠的分布式计算机系统应该如何工作的隐喻。基本上，如果分布式系统中的一个节点发生故障，系统的其余部分需要做出反应，以防止整个系统关闭。其他节点需要就该组件是否出现故障并需要移除达成**共识**(当系统能够做到这一点时，它就是[拜占庭容错](https://en.wikipedia.org/wiki/Byzantine_fault_tolerance))。

在以前的帖子中，我们已经探讨了节点的分布式本质，这些节点必须协调(并合作)以保护区块链(即工作证明[共识机制](/@PaulApivat/newcomers-guide-to-the-bitcoin-white-paper-bb9e7aaada06/#f2dc))，而不必知道彼此的身份或信任彼此。这本质上是所有寻求共识的区块链必须解决的拜占庭将军式的问题。

[本文](https://people.eecs.berkeley.edu/~luca/cs174/byzantine.pdf)描述了为什么这具有挑战性，以及为什么实际上解决了这个问题的比特币代表了一种突破性的创新。

人们已经提供了许多关于 BGP 如何与区块链相关的描述([此处](/loom-network/understanding-blockchain-fundamentals-part-1-byzantine-fault-tolerance-245f46fe8419)，[此处](/all-things-ledger/the-byzantine-generals-problem-168553f31480))，所以我想集中讨论本文的一个特定部分，并强调中本聪如何在解决 BGP 的过程中诞生了**密码经济学**——这是一个我们将在后续帖子中更深入探讨的话题。

## 密码经济学诞生了

BGP 描述了一个虚构的场景，拜占庭将军正在围攻一个敌人的城市。他们必须在几个阵营之间协调，要么进攻，要么撤退，不能三心二意，因为那意味着必然失败。指挥官必须向他的将军们发送消息(例如，“上午 10 点进攻”或“下午 3 点撤退”)，所有这些都在城市周围的不同营地中。

如果所有的将军都是诚实的，这将是一个简单的协调活动，但在这种情况下，这里是分布式计算的应用程序，有不诚实的，背叛的将军(甚至间谍)可能会选择通过发送错误的消息来扰乱地面部队，从而破坏任务。

本质上，所有的将军(节点)必须对来自指挥官的信息达成**共识**，尽管潜在的叛徒/间谍主动发送错误的信息(更多细节见[此处](/loom-network/understanding-blockchain-fundamentals-part-1-byzantine-fault-tolerance-245f46fe8419)、[此处](/all-things-ledger/the-byzantine-generals-problem-168553f31480))。

当 Lamport，Shostak，Pease，1982 年描述计算系统的 BGP 解决方案时，几个假设引起了我的注意:

1.  假设能够通信的无故障节点(诚实的将军)将传递(非伪造的)消息。在比特币中，防止签名信息的伪造是通过[数字签名](/@PaulApivat/newcomers-guide-to-bitcoin-security-a-closer-look-at-cryptography-a691ab09a726/#290d)实现的。
2.  有故障的节点(不诚实的将军)不能冒充无故障的节点(诚实的将军)。[公钥加密](/@PaulApivat/newcomers-guide-to-bitcoin-security-a-closer-look-at-cryptography-a691ab09a726/#094f)帮助我们满足这个条件(即，如果你不给出你的私钥，没有人能冒充你)。
3.  消息的缺失可以通过其未能在固定的时间长度内到达来检测。Nakamoto 通过时间戳系统解决了这个问题(参见此处的该时间戳如何在**工作证明**中)。

Nakamoto 使用密码学来解决解决 BGP 的条件，并进一步提供激励来阻止不诚实的(未来)行为。工作验证不仅解决了时间同步问题，更重要的是，有意迫使人们花费巨大的计算能力和能量来阻止他们做出任何偏离共识的行为(例如，由于开始新的链而缺少消息)。

这个讨论假设网络中诚实节点和不诚实节点的混合。Lamport 等人(1982 年)建议“至少三分之二”的节点必须诚实；其他人**拒绝**这种[诚实多数假设](/blockchannel/a-crash-course-in-mechanism-design-for-cryptoeconomic-applications-a9f06ab6a976/#6966)提供了对敌对环境假设的洞察。

我个人认为这个空间的人并不认为人从根本上就是腐败的，而是认为，在设计大规模系统的时候，最好 [*“尽量减少对陌生人善意的依赖”*](https://twitter.com/NickSzabo4/status/984550408751276032) *。(见* [*亦作*](https://unenumerated.blogspot.com/2006/10/hello-kitty-people.html) *)*

最后，中本聪不仅解决了拜占庭将军的问题，还解决了他是如何解决这个问题的，他将密码学和经济激励结合起来，为这个领域的所有后续创新奠定了基础。