# 区块链私人频道是一个矛盾体

> 原文：<https://medium.com/coinmonks/blockchain-private-channel-is-an-oxymoron-2ec30c38b095?source=collection_archive---------3----------------------->

![](img/e7015c453587e51c2a90041d8c942284.png)

专用信道是一个通用术语，用来描述使用区块链分类帐与网络参与者子集进行通信的方法。通信模式与常规区块链相同，只是对等通信通过 TLS 连接加密，并在端点解密。

在 Quorum v2.0.2 中，这些通信被称为私有事务。 [](http://2.	https://github.com/jpmorganchase/quorum-docs/blob/master/Quorum%20Whitepaper%20v0.1.pdf) 在 Hyperledger Fabric v1.1 中同样的约定称为渠道。[](http://1.	https://hyperledger-fabric.readthedocs.io/en/release-1.1/channels.html)

从理论上讲，实现拜占庭容错共识的节点正在进行私有交易。Quorum v.2.0.2 使用两种独立的共识机制，可在部署时选择。第一个是 Raft 共识模型，第二个是伊斯坦布尔拜占庭容错(IBFT)，它是实用拜占庭容错(PBFT)的修改衍生物。 [](http://3.	https://github.com/jpmorganchase/quorum) [⁶](http://6.	https://github.com/ethereum/EIPs/issues/650)

以下是上述共识机制的方程式。在这些等式中， **N** 是总节点数，而 **F** 是包含的故障节点数。对于值 **F** 而言， **N** 的结果值是达到共识所需的节点数。

**PBFT 共识容错方程** [**⁴**](http://4.	http://pmg.csail.mit.edu/papers/osdi99.pdf)

> **N** = 3 **F** +1

**筏共识容错方程** [**⁵**](http://5.	https://www.cl.cam.ac.uk/techreports/UCAM-CL-TR-857.pdf?__hstc=137489263.d50a3c91e72c280a7921bf0d7ab734f9.1522540800074.1522540800075.1522540800076.1&__hssc=137489263.1.1522540800077&__hsfp=1773666937)

> **N** = 2 **F** +1

实际上，由于缺少参与节点，这些通道无法实现拜占庭容错。在一般情况下，私密性的要求意味着在私密信道中将只有两个参与节点。如果在 PBFT 专用信道中有单个故障节点，则需要 4 个参与节点。同样，如果 Raft 专用信道中有单个故障节点，则需要 3 个参与节点。

卡斯特罗-利斯科夫 99 年的论文中详细阐述了同样的易错性，其中指出，

> 该算法没有解决容错隐私的问题:有缺陷的复制品可能将信息泄露给攻击者。在一般情况下提供容错隐私是不可行的，因为服务操作可能使用它们的参数和服务状态执行任意计算；副本需要这些信息才能高效地执行此类操作”。 [⁴](http://4.	http://pmg.csail.mit.edu/papers/osdi99.pdf)

# **因此，没有已知的方法在区块链的共识层实现隐私。**

私有通道模式的捍卫者可能会参考 Quorum 使用的检查点技术。通过将来自全局可见链的块散列插入到新的私有块中，在私有通道中采取检查点措施，反之亦然，将每个新的私有块的块散列插入到全局可见链中。检查点过程确保私有链的出处是全局可审计的。

这种技术不是有效缓解的原因是私有通道只能在达成私有共识之后向全局链添加新的检查点。如前所述，在存在单个故障节点的情况下，双方专用信道无法达成共识。

# **有效的缓解技术包括**

> 1.不要使用私人频道参加少于 4 人的 PBFT 派对和 3 人的 Raft 派对。
> 
> 2.在渠道中加入惰性方，比如来自全球供应链的中立观察者。
> 
> **3。不要使用私人渠道。**

# **参考文献**

1.[https://hyperledger-fabric . readthedocs . io/en/release-1.1/channels . html](https://hyperledger-fabric.readthedocs.io/en/release-1.1/channels.html)

2.[https://github . com/jpmorganchase/Quorum-docs/blob/master/Quorum % 20 白皮书%20v0.1.pdf](https://github.com/jpmorganchase/quorum-docs/blob/master/Quorum%20Whitepaper%20v0.1.pdf)

3.[https://github.com/jpmorganchase/quorum](https://github.com/jpmorganchase/quorum)

4.[http://pmg.csail.mit.edu/papers/osdi99.pdf](http://pmg.csail.mit.edu/papers/osdi99.pdf)

5.【https://www.cl.cam.ac.uk/techreports/UCAM-CL-TR-857.pdf? _ _ hstc = 137489263 . d 50 a3 c 91 e 72 c 280 a 7921 BF 0d 7 ab 734 f 9.1522540800074.1522540800075.1522540800076.1&_ _ hssc = 137489263.1 . 1522540800077&_ _ hsfp = 137489263.1 .

6.【https://github.com/ethereum/EIPs/issues/650 

[来自 Pexels 的 Manuel Geissinger 的照片](https://www.pexels.com/photo/interior-of-office-building-325229/)