# 索特里亚的索拉纳虫赏金猎人

> 原文：<https://medium.com/coinmonks/solana-bug-bounty-hunting-with-soteria-1b8b75c741de?source=collection_archive---------3----------------------->

最近，使用 [Soteria](/coinmonks/soteria-a-vulnerability-scanner-for-solana-smart-contracts-cc202cf17c99) 我们发现了一个链上 Solana 智能合约( [jet-v1](https://www.jetprotocol.io/) )中的漏洞，并通过 [Immunefi](https://immunefi.com/) 获得了一笔漏洞奖金。我们感谢 Jet 礼宾团队和 Immunefi 的快速响应和慷慨支持。该修复已在[提交](https://github.com/jet-lab/jet-v1/commit/7aa717029cd77b772065f3213849c86e19a50298)中应用。

这篇文章分享了我们用 Soteria 寻找 bug 的经验。

# 索特里亚是什么？

Soteria 是一个由 GreenCore 技术驱动的自动化验证工具。它扫描合同源代码，寻找常见的陷阱…