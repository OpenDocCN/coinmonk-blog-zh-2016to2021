# Hyperledger 结构中的系统链码-VSCC、ESCC、LSCC、ESCC、QSCC

> 原文：<https://medium.com/coinmonks/system-chaincodes-in-hyperledger-fabric-vscc-escc-lscc-cscc-a48db4d24dc3?source=collection_archive---------3----------------------->

![](img/eab1f43c2abe18d7c563a1a859ac9586.png)

系统链码也与用户链码相同。这些都内置在对等的可执行文件中。

Hyperledger fabric 中提供了以下系统链代码。

*   **LSCC**
*   **ESCC**
*   **VSCC**
*   **CSCC**
*   **QSCC**

> [发现并回顾最佳区块链软件](https://coincodecap.com)

1.  **LSCC:生命周期系统链码**

该系统链码用于管理链码的生命周期。它用于安装、实例化和更新链代码。当我们安装 chaincode 时，这个过程要经过 LSCC

ESCC:签注系统链码。

该系统链码用于通过对响应进行数字签名来认可交易。
客户在背书环节发送交易建议调用链码功能。客户用他的证书签署提案，并发送给背书的同行。第一批签对等方模拟交易，并验证客户是否有权在通道上调用交易。背书对等体执行链码并创建读/写(R/W)集。事务结果包括响应值、读集和写集。然后背书对等点调用 ESCC，用对等点身份在这个交易响应上签名并发送给客户端。

**3。VSCC:验证系统链码**

这用于根据其背书政策验证交易。

验证系统链码对照为链码指定的背书策略评估交易中的背书。如果不满足背书策略，则该交易被标记为无效。

当所有对等体从订购者处获得块时，它们首先验证订购者在块上的签名。每个块都被解码，所有的交易在执行 MVCC 验证之前都要经过 VSCC。VSCC 根据为链码指定的背书策略验证事务处理中的背书。如果背书策略未能满足，则交易被标记为无效。

注:**MVCC**——多版本并发控制——保证交易在背书阶段读取的密钥版本与提交时本地账本中的当前状态一致。它在由 VSCC 验证的块中的所有有效交易上执行。如果读取的集合版本不匹配，则事务被标记为无效。 **MVCC 不是链码，它是一种避免超分类账结构中重复支出问题的机制**

**4。CSCC:配置系统链码**

该链码用于管理通道配置。当我们创建一个通道，更新通道，这个过程通过配置系统链码。实际上，它验证了通道的创建/更新。

5.QSCC:查询系统链码

该链代码在所有对等体中运行，并提供分类帐 API，如 getTransactionByTransactionID、getBlockByBlockNumber、getblockbytsid、GetChainInfo、getblockbytsid。

希望这篇文章能帮到你。这是关于系统链码的基本信息。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)