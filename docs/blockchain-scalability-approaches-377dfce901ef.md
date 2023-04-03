# 区块链可扩展性方法

> 原文：<https://medium.com/coinmonks/blockchain-scalability-approaches-377dfce901ef?source=collection_archive---------4----------------------->

![](img/fb5efb45a14f4fbebce1f287d3de3a57.png)

至少有 4-5 种可扩展性方法，其中 2-3 种会成功，它们的成功是倍增的，也就是说，如果 3 种方法成功了，那么将所有这些方法结合在一起的东西将会取得巨大成功。
如果我们从广义上看这些方法，它们是:

## 1)设计更好的一致性算法:

如果我们观察所有的网络，就会发现有两个基本的限制因素:

1.  带宽—我们有许多节点，这些节点必须相互发送数据块，而带宽是有限的，因为如果数据块太大，它们在网络中的传播速度就不够快。
2.  处理能力——我们不希望节点是一个非常强大的服务器(以避免采矿集中化)，我们希望它们是一台普通的机器，但一台普通的机器每秒只能处理这么多事务。

    我们可以通过改进 consensus 算法来提高每秒的交易数量，所以如果我们看看比特币，POW 需要很长的阻塞时间，即 10 分钟，但最终会浪费大量的备用带宽。
    例如，我的比特币节点收到一个区块，在 20 秒内处理它，然后等待下一个区块解决 10 分钟，什么也不做。
    我的节点在 9 分 40 秒内无法工作，所以比特币无法有效利用节点的带宽和处理能力。

    这就是第一类方法的目标，即设计更好的东西，更好地利用节点的带宽和处理能力。POS，Casper，PBFT(在 Cosmos 等中使用)，Hashgraph 的共识算法，这些共识协议的基本优势是它们更有效地使用带宽和处理能力。
    假设您将成为一个 cosmos 节点，您的节点将持续发送和接收事务，即接收、签名、发送，并且它将始终保持忙碌状态。这使得它们的可扩展性比比特币高出 10 到 100 倍。这种方法肯定会出现，因为这在工程上是可以解决的。

## 2)正确块处理的证明(例如 Tezos):

假设您在印度有一个节点，我有另一个节点，您正在参与某个共识协议&您下载了一个巨大的数据块，比如一个 20 GB 的数据块，我的节点验证了 20 GB 数据块中的核算是正确的。通常在比特币中发生的事情是，你将那个块广播给我，我也将验证那个 20 GB 的块，但是使用被称为零知识证明(或简洁的计算证明)的工具集，我们可以做一些有趣的事情。
可能发生的情况是，您下载一个 20 GB 的数据块，验证该数据块，然后创建一个简洁的证明，基本上说明您的节点下载了该数据块并进行验证，发现它是正确的，这就是您正确执行验证的证明。
然后，当您发送 20 GB 数据块并发送证明时，我不再需要验证这些价值 20gb 的数据，我可以假设它们是正确的，因为我得到了证明。
如果一个节点验证块中的记账，网络的其余部分可以免费使用该工作，这将节省大量的处理能力。
例如，对于一个 1 MB 的比特币块，10k 个节点需要处理这个块。

将来，它将只是一个 100GB 的数据块，只有一个节点将验证这个 100 GB 的数据块&其余的节点将免费使用这个数据块。

## 3)使用多个区块链

这就是我们应该有数百个区块链，每个区块链应该并行处理事务的想法(例如:cosmos)。
比方说，Alice & Bob 是企业家，推出了他们自己的区块链，上面还有一枚硬币，即 Bob 硬币& Alice 硬币。然后，我们教导区块链以这样的方式相互通信，如果有一个用户有 1000 个 Bob 硬币，该用户可以将 1000 个 Bob 硬币从 Bob 链移动到 Alice 链；利用 Alice chain 上的应用程序，然后返回 Bob chain。

我们可以有数百个区块链，每个并行处理—因此，如果一个区块链每秒处理 100 个事务，而有 1000 个这样的区块链并行工作，我们每秒总共可以处理 100，000 个事务。
这就是所谓的连锁互联网方法&我们可能会在今年看到这项技术的应用。

## 4)在单个区块链中实现高效的智能签约(例如，Rchain)

以太坊的工作方式是假设我们在印度&有一群印度人在四处发送以太，然后有一群中国人参与他们的当地社区。
全球以太坊网络必须订购所有印度和中国的交易，即它需要就所有这些交易的顺序达成共识，例如:
交易一个来自印度，两个来自中国，三个来自中国，四个来自印度&等等……

Rchain 将允许印度的以太坊经济&中国独立运营。
只有印度和中国之间的交易才需要订购并达成共识。
这减少了主链上需要处理的交易量。
如果今天以太坊正在处理 100 万笔交易，我们可以将其缩减为只需要处理 1 万笔交易，同时达到相同的效果，因为许多交易是相互独立的。
所以 Rchain like 方法基本上可以收缩交易的数量。

## 5)链外方法:闪电网络、雷电网络、Truebit

在某种程度上，所有这 5 项都将成功，所有这些事情都将成倍增加，更好的共识= 100 倍，交易数量减少= 100 倍，互操作性= 1000 倍，离线= 100 倍& zksnarks 是另一个 100 倍。
如果我们以某种方式将所有这些都增加，我们的交易能力将增加 100 万倍，这足以满足未来 10 年的需求。

附:这篇文章摘自我对震中的 Meher Roy 的采访。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)