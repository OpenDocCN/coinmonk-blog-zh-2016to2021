# 理解 Rust 和 Solana 智能合约中的算术溢出/下溢

> 原文：<https://medium.com/coinmonks/understanding-arithmetic-overflow-underflows-in-rust-and-solana-smart-contracts-9f3c9802dc45?source=collection_archive---------0----------------------->

整数溢出/下溢在智能合约中出奇地常见，因为区块链应用程序经常对金融数据进行数学计算。

Rust 是在区块链使用的一种流行语言，比如 Solana 和 T2 Polkadot。对于许多开发人员来说，Rust 是内存安全的，所以它没有算术溢出/下溢，这可能是一种误解。本文解释了为什么 Rust 程序仍然遭受算术错误，这些问题如何影响区块链安全性，以及如何在智能…