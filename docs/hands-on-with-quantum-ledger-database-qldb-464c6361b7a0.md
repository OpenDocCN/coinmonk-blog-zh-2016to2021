# 实际操作 Quantum 分类帐数据库(QLDB)

> 原文：<https://medium.com/coinmonks/hands-on-with-quantum-ledger-database-qldb-464c6361b7a0?source=collection_archive---------0----------------------->

![](img/dee585ef91a62a9afdb5e9461447c0dd.png)

Image source: [Groupemiage](http://groupemiage.net/bachelor-europeen-dees-lic-pro-bac-3/bachelor-europeen-en-developpement-et-base-de-donnees/database-770x481/)

在[之前的文章](/devopsinternationalbv/what-is-this-quantum-ledger-database-that-im-keep-hearing-about-a3c16c35c799)中，我已经解释了什么是 quantum ledger 数据库、好处、用例，以及它与常规数据库的不同之处，以及它与[区块链](https://blog.coincodecap.com/what-is-blockchain-a-simple-guide-for-dummies/)的关系。本文将展示如何设置 Amazon 的 QLDB，并在我们的 Nodejs 应用程序中将它用作数据库。我认为这是一个更现实的实现。希望本指南能够帮助您“快速入门 QLDB ”,而无需…