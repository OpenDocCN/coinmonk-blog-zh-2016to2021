# 如何审计 Solana 智能合同第 4 部分:锚框架

> 原文：<https://medium.com/coinmonks/how-to-audit-solana-smart-contracts-part-4-the-anchor-framework-ef42d944f086?source=collection_archive---------0----------------------->

继[第 3 部分:渗透测试](/coinmonks/how-to-audit-solana-smart-contracts-part-3-penetration-testing-a315b3bbb2d3)之后，本文介绍了 [Anchor](https://github.com/project-serum/anchor) 的内部结构，这是一个用于编写和测试 Solana 智能合约的流行框架。

就像以太坊的[松露](https://www.trufflesuite.com/)一样， [Anchor](https://github.com/project-serum/anchor) 为在 Solana 上开发完整的应用程序提供了一系列功能，特别是:

*   **Rust crates(宏)**用于编写索拉纳程序
*   [**IDL(接口描述语言)**](https://en.wikipedia.org/wiki/Interface_description_language) 规范
*   **CLI 和工作区管理**