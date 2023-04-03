# 论高效以太坊存储

> 原文：<https://medium.com/coinmonks/on-efficient-ethereum-storage-c76869591add?source=collection_archive---------0----------------------->

![](img/cd3ec9655d8071be2ba51b847e4c0d96.png)

当你在尽可能降低汽油成本方面取得进展时，你会不可避免地发现，连锁仓储往往是最大的支出。有许多行之有效的方法可以降低这些成本，比如对照根哈希来验证 [Merkle 证明](https://blog.ethereum.org/2015/11/15/merkling-in-ethereum/)，[存储 IPFS 哈希](/@didil/off-chain-data-storage-ethereum-ipfs-570e030432cf)而不是直接将数据放在链上，以及[将多个值打包成单个 32 字节的字](/@novablitz/storing-structs-is-costing-you-gas-774da988895e) *(或许可以通过利用* [*高效的*](/coinmonks/on-efficient-ethereum-addresses-3fef0596e263)……