# 如何审计索拉纳智能合同第 1 部分:一个系统的方法

> 原文：<https://medium.com/coinmonks/how-to-audit-solana-smart-contracts-part-1-a-systematic-approach-56a434f6c9ed?source=collection_archive---------0----------------------->

索拉纳是受欢迎的爆炸，由于其最低的交易费。随着越来越多的高价值 Dapps 建立在 Solana 之上，为它们开发审计技术是至关重要的。

然而，与以太坊(在过去三年中建立了审计规则)相比，审计 Solana 智能合同需要新的和更高级的技能。索拉纳在两个主要方面不同于以太坊:

(1) [它使用不同的语言——Rust](https://docs.solana.com/developing/on-chain-programs/developing-rust)(相对以太坊中的 Solidity)和