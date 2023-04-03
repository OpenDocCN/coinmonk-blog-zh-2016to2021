# 以太坊 2.0 vs 符号(第七部分):

> 原文：<https://medium.com/coinmonks/ethereum-2-0-vs-symbol-part-7-9c6d9d2be8c0?source=collection_archive---------10----------------------->

## 结论

![](img/1805de73f8b77bb29579c076565c3e75.png)

有什么灵丹妙药可以解决我们所有的区块链难题吗？

在之前的 6 篇文章中，我一直在并排比较以太坊和 Symbol 的特性，我只是触及了皮毛。概括一下:

1.  第一部分:简单地谈了一下他们的起源和发展轨迹。
2.  [第二部分](/coinmonks/ethereum-2-0-vs-symbol-part-2-consensus-protocols-73e0a2589242?sk=cd76097533495f30abb79c65cd693107):讲述了两个区块链是如何用不同的方式实现利益相关证明的。
3.  [第三部](/coinmonks/ethereum-2-0-vs-symbol-part-3-442b0d34c857?sk=e9d448005c6e42d7907a48325af3b3fe):讨论以太坊的智能契约和符号插件。
4.  第 4 部分:研究了区块链中不同类型的节点及其角色。
5.  第 5 部分:讨论了可替换的令牌在区块链中是如何表示的。
6.  [第 6 部分](/coinmonks/ethereum-2-0-vs-symbol-part-6-non-fungible-tokens-42290ce34854?sk=50cc65cd0cd3dcbda78dd993f6209b6c):讨论了不可替换的令牌在两个区块链中是如何表示的。

还有很多话题可以聊。举几个例子:

## Tokenomic

Genesis 区块生产了约 7200 万个醚，目前生产了近 1 . 15 亿个醚。以太坊没有比特币那样的对半机制，即使以太坊也在使用铸造以太的工作证明。新以太网的供应正在减少，并计划随着以太坊 2.0 的实施而进一步减少。

另一方面，Symbol 的初始令牌供应量约为 78 亿，并将通过块奖励逐渐增加其总供应量，直到其最大供应量为 90 亿减 1，与 NIS1 匹配。增量将与比特币的开采大致同时结束。

## 交易费用

以太坊交易费以气计算，以以太支付。汽油是你把车从 A 点开到 B 点所需的汽油量，而汽油价格是汽油的价格。您可以设置您愿意为特定交易支付的最大燃气费。

[符号交易费用](https://docs.symbolplatform.com/concepts/fees.html#transaction-fee)由交易的大小乘以乘数确定，乘数由获取包含交易的块的节点设置。符号交易费以其本国货币 XYM 支付。

从这个角度来看，以太坊中的天然气是交易规模，如符号中所示，而以太坊中的天然气价格是节点乘数，如符号中所示。交易费本质上是 tokenomic 的一部分，就像令牌的分配一样，它激励节点所有者和生态系统。为了使费用更合理，并控制乙醚的总供应量，建议采用 ERC-1559。

## 第 2 层扩展/链外处理

又名兔子洞。为了有助于区块链的扩展，而不使用户负担更高的费用和更高的硬件要求，并且减慢区块链，提出了脱离主链的方法来处理不是区块链的核心服务的服务。

## 独特性

以太坊就像一张空白的画布，你可以用任何你能想到的东西在上面泼洒。(所有 ERC 标准都像水印一样，可以作为指导方针。)它的图灵完备语言——Solidity——和以太坊虚拟机是它的标志。

符号，或 NEM，就像秘密花园填色书。它为你提供了指导方针，但你可以自由地发挥你的想象力。它的重要性评分系统、节点信誉和收获机制都是独一无二的。

还有很多要谈的:区块链之间的互操作性、用例、未来的发展和挑战。我们把这个留到下一轮。

当以太坊 2.0 阶段 0 时，信标链已经发布，其他阶段仍在进行中。区块链生态系统的成员期待宁静号的到来。

今天，【2021 年 3 月 17 日，象征 Mainnet 上线！( [NIS1 快照【2021 年 3 月 12 日 04:26:34 (UTC)在区块高度 3105500)。更多激动人心的使用案例即将推出。](https://nem.io/news/symbol-snapshot-complete/)

![](img/8ffdb907ddefa359e3326423d56e591a.png)

要回答文章开头的问题，没有一个区块链能为所有人服务。技术的美好和真实在于它的不断进步。人类的欲望是无止境的，呈指数增长的。没有完美的技术会永远完美。

继续发明！

直到下一个系列！再见。

*参考文献:*

1.  [https://coinfomania . com/does-ether eum-have-halving-lilke-bit coin/](https://coinfomania.com/does-ethereum-have-halving-lilke-bitcoin/)
2.  [https://blog . quill hash . com/2021/02/05/significance-of-EIP-1559-part-4-defi-in-out/](https://blog.quillhash.com/2021/02/05/significance-of-eip-1559-part-4-defi-in-out/)
3.  [https://decenter . org/en/what-are-gas-gas-limit-and-gas-price-in-The-The-The-ether eum-network #:~:text = The % 20 gas % 20 limit % 20 is % 20 The，on%20each%20unit%20of%20Gas](https://decenter.org/en/what-are-gas-gas-limit-and-gas-price-in-the-ethereum-network#:~:text=The%20Gas%20Limit%20is%20the,on%20each%20unit%20of%20Gas)
4.  【https://docs.symbolplatform.com/ 
5.  [https://defiprime.com/ethereum-l2](https://defiprime.com/ethereum-l2)
6.  [https://nem platform . com/WP-content/uploads/2020/05/NEM _ tech ref . pdf](https://nemplatform.com/wp-content/uploads/2020/05/NEM_techRef.pdf)