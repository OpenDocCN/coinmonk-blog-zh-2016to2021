# 如何审计 Solana 智能合同第 3 部分:渗透测试

> 原文：<https://medium.com/coinmonks/how-to-audit-solana-smart-contracts-part-3-penetration-testing-a315b3bbb2d3?source=collection_archive---------0----------------------->

在本文中，我们介绍了一些渗透测试工具来帮助检测 Solana 或 Rust 程序中的漏洞。

*   [**Solana PoC 框架**](https://github.com/neodyme-labs/solana-poc-framework) :由 [Neodyme](https://neodyme.io/) 开发的为 Solana 智能合约创建 PoC 的框架。
*   [**AFL . RS**](https://github.com/rust-fuzz/afl.rs)**:**一个基于 [AFL](http://lcamtuf.coredump.cx/afl/) 的 Rust 程序模糊测试工具
*   [**cargo-fuzz**](https://github.com/rust-fuzz/cargo-fuzz)**:**`cargo`子命令，用于使用`libFuzzer`和 LLVM 消毒剂进行模糊处理。
*   [](https://github.com/xd009642/tarpaulin)****:**Rust 项目的代码覆盖报告工具**