# 富数据:将可证明的 Oracle 集成到以太坊智能合约中

> 原文：<https://medium.com/coinmonks/rich-data-integrating-provable-oracle-into-ethereum-smart-contracts-43a434b8a327?source=collection_archive---------3----------------------->

就其本质而言，智能合约是自包含的代码脚本，这意味着它们本质上无法访问外部信息，如 web APIs 或文件系统。这有一个很好的理由:在[以太坊](https://blog.coincodecap.com/tag/ethereum/)的例子中，这完全是关于决定论和可验证的数据。

当某人成功发布智能合约时，它会在维护区块链完整副本的每台机器上执行。重要的是，这种执行在所有机器上保持一致，以便得到相同的结果…