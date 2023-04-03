# IPFS 四分钟白皮书

> 原文：<https://medium.com/coinmonks/ipfs-whitepaper-in-four-minutes-b3d5eb0e75c6?source=collection_archive---------4----------------------->

*本文是名为*[*info urminds*](http://infourminutes.co/)*的每周系列的一部分。他们每周都会发布一份密码摘要或区块链白皮书。请在此* *订阅* [*。*](https://infourminutes.us14.list-manage.com/subscribe/post?u=94a41e60921ac2e7b1bd1945e&id=c8ed5bd2de)

# 简介:

今天，HTTP 是事实上通过互联网传输文件的方式。移动小文件非常有效，因为这个过程很便宜。但是，HTTP 没有利用过去十年中发明的新的文件分发技术。如果不打破向后兼容性，升级几乎是不可能的，因为对当前 HTTP 和 web 模型的巨大投资。

在接下来的日子里，将会有新的挑战，例如:

*   移动数 Pb 大小的数据集。
*   高容量实时媒体流。
*   永久性(防止重要文件丢失)。

几乎每个问题都归结于高可用性和更大的规模。星际文件系统(IPFS)是一个对等版本控制的文件系统，旨在解决这些挑战。

为了理解 IPFS 是如何工作的，有必要理解什么是分布式哈希表(DHT)。

# 分布式哈希表:

哈希表是一种表示键值对数组的数据结构。它使用散列函数来计算索引/密钥。哈希表有助于根据关键字快速查找值。

分布式哈希表(DHT)是一种分布式系统，提供对键值存储的访问。该存储分布在参与节点上，提供了出色的性能和可伸缩性。分布式哈希表广泛用于对等系统中，以协调和维护关于系统的元数据。

![](img/fb6266e433dc30bd962703f14d0ab5a9.png)

*IPFS network illustration*

# IPFS 节点:

**NodeId** 标识 IPFS 系统中的一个节点。NodeId 只不过是其公钥的散列。节点可以随时改变它们的 NodeId，但是它们被激励保持不变。这些节点将(它们感兴趣的)对象存储在它们的本地存储器中。这些对象代表 IPFS 中的文件和其他数据结构。

所有节点都维护一个分布式哈希表，用于查找:

*   网络中其他对等体的网络地址，以及
*   可以为特定对象服务的同行

这个分布式哈希表允许 IPFS 找到可以服务一个对象的对等体，并且能够通过网络到达对等体。

![](img/85227fd3f695bfc6add01a78efcd2522.png)

*IPFS finding object from network peers*

# 内容寻址:

IPFS 使用不同的内容寻址方法来识别内容。这种方法不同于现在的 web，在 web 中，包含对象的服务器处理内容。

IPFS 内容地址解析为包含 IPFS 链接列表和一些内容数据的 IPFS 对象。当向 IPFS 添加大文件时，它被分成许多小块，地址解析为指向这些小块的 IPFS 链接列表。这种内容寻址方法确认地址将总是返回相同的文件。

这种方法的另一个好处是，只要其中一个节点有内容，就可以从 IPFS 网络访问它。这解决了当今互联网中存在的死链接问题。重复文件不会占用多倍的空间，因为它将始终指向相同的内容哈希。网络中只有一个副本就足以从网络中检索它。

# IPFS:内容寻址对等文件系统

给定内容地址，IPFS 网络用具有所需对象的对等体进行响应。可以从多个对等体同时接收对象及其链接。

在 IPFS 从网络返回对象后，我们的对等体可以共享它。该节点还可以验证内容没有被篡改，因为散列值映射到 IPFS 网络中的内容。因为文件的内容生成内容地址(哈希)，所以每当文件被修改时，内容地址就会改变。

每次文件改变时共享新的内容地址是不方便的。为了解决这个问题，IPFS 提出了一个名为 **IPNS 的概念，或者叫星际命名系统。**

# 使用案例:

IPFS 提供了一种通用的方式来以对等的方式共享文件和数据，从而优化交付。IPFS 非常适用的一些用例场景包括:

*   共享文件和巨大的数据集。
*   为 IPFS 的网站和博客提供服务。
*   在数据处理工作流中用作数据源或接收器。
*   使用作为挂载的文件系统。

# 结论:

白皮书讨论了一种对等的、版本控制的文件系统方法。这个文件系统没有单点故障。由于数据是内容寻址的，网络中的节点不需要相互信任。根据[这篇文章](https://ipfs.io/ipfs/QmNhFJjGcMPqpuYfxL62VVB9528NXqDNMFXiqN5bgFYiZ1/its-time-for-the-permanent-web.html)，IPFS 有潜力取代 HTTP，使网络成为分布式的。

# 参考资料:

*   [IPFS 白皮书](https://github.com/ipfs/ipfs/blob/master/papers/ipfs-cap2pfs/ipfs-p2p-file-system.pdf?raw=trueIPFS)
*   [使用案例 Github 问题— ipfs/ipfs#230](https://github.com/ipfs/ipfs/issues/230)
*   [HTTP 已经过时。现在是分布式永久网络的时候了](https://ipfs.io/ipfs/QmNhFJjGcMPqpuYfxL62VVB9528NXqDNMFXiqN5bgFYiZ1/its-time-for-the-permanent-web.html)

本故事由阿维纳什·昆达利亚撰写，最初发表于[](http://infourminutes.co/whitepaper/ipfs)**资讯。**

**要阅读白皮书的每周摘要，请订阅* [*邮件列表*](http://eepurl.com/dkTrvz) *。**

*你在推特上吗？请 [*关注我们*](https://twitter.com/infourminutesco) *保持更新。**

*[![](img/9d382e3e2422e87d2920b3e4874173c4.png)](https://medium.com/coinmonks/ipfs/home)

**Click to read more about IPFS***