# 如何审计 Solana 智能合同第 2 部分:自动扫描

> 原文：<https://medium.com/coinmonks/how-to-audit-solana-smart-contracts-part-2-automated-scanning-ceb88830ae6d?source=collection_archive---------1----------------------->

继[第 1 部分:系统方法](https://blog.supercompiler.xyz/how-to-audit-solana-smart-contracts-part-1-a-systematic-approach-56a434f6c9ed)之后，本文介绍了一些自动扫描工具来帮助审计 Solana 智能合同。

*   [**索特里亚**](/coinmonks/soteria-a-vulnerability-scanner-for-solana-smart-contracts-cc202cf17c99):Solana 程序专用扫描器，可发现所有常见的安全漏洞(如漏检、算术溢出等)。
*   [**cargo-audit**](https://docs.rs/cargo-audit/) :审计 Cargo.lock 文件中已知的安全漏洞
*   [**cargo-clippy**](https://github.com/rust-lang/rust-clippy) :捕捉常见错误的 lints 集合。
*   [](https://github.com/rust-secure-code/cargo-geiger)****:**报告…的使用情况**