# 为 Solang 编译器实现未用变量消除和未定义变量检测

> 原文：<https://medium.com/coinmonks/implementing-unused-variable-elimination-and-undefined-variable-detection-for-the-solang-compiler-73f59a626921?source=collection_archive---------11----------------------->

![](img/b40fff45c110aecd40eac6f2238001ca.png)

Photo by [Clint Adair](https://unsplash.com/@clintadair?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

2021 年 6 月，我通过 Linux 基金会申请了一个导师，并被接受为 [Solang 编译器](https://github.com/hyperledger-labs/solang)做出贡献，并实现了未使用变量消除、未定义变量检测和公共子表达式消除。在本文中，我将解释我的未使用变量消除和未定义变量检测技术。

Solang 是一个针对 Solana、Substrate 和 ewasm 的 Solidty 编译器。它是用 Rust 编写的，使用 LLVM 作为后端。它允许我们在调用 LLVM 之前为 Solidity 语言编写许多优化。Solang 还处于早期阶段，但是已经有了一些编译器基础设施，比如解析树、抽象语法树(AST)和控制流图的中间表示(CFG)。它也有一些优化，如不断折叠和强度减少。

目前还没有未使用变量消除和未定义变量检测的实现，所以实现这一点是我作为学员的工作。第一个目标是检测未使用的变量。每个局部变量都放在一个符号表中(参见 Github 要点)，这样我们可以在整个代码中跟踪它的引用。

反过来，存储变量在每个合同中使用变量向量来处理:

在 Solang 的语义分析过程中，我们遍历解析树并构建抽象语法树。每当我们到达一个表达式节点时，我们可以检测到表达式中引用的任何变量。对于表达式节点，我指的是表示基本表达式的节点，如加法、减法、布尔 AND 等。对于表达式中的变量名，我们在符号表中查找变量名的索引，并创建一个引用变量索引的 AST 节点。同样，存储变量 AST 节点包含它在契约变量 vector 中的索引。

这些变量索引允许我们检索对这些实体的引用，这样我们就可以发出信号，表明变量已经在 Solidity 代码中被读取。使用上述相同的推理，我们可以在解析树遍历期间识别赋值，并利用变量节点将变量标记为已赋值。

用于识别未使用变量的逻辑也可用于检测从未发出的事件定义。这里的主要区别在于事件是在*名称空间*中的事件向量中被跟踪的，如下所示。

在语义分析之后，我们遍历符号表、契约的变量向量和名称空间的事件向量，以识别未使用的变量和事件，并发出编译警告。

重要的是要提到这里有一个边缘案例。我们不能将公共契约变量标记为未使用，因为它们可以通过生成的访问函数直接访问，我们可以在区块链事务中调用该函数，即使它们在 Solidity 代码中没有被引用。

在确切地知道哪些变量从未被使用过之后，我们可以实现未使用变量消除。我们的代码允许 Solang 从中间表示中删除以下情况:

*   从未被读取或赋值的变量。
*   已经赋值但从未读取的变量。
*   未使用变量的赋值，包括析构函数。
*   表达式中未使用变量的赋值(如`a = b + (c=1)`)

为此，我们在代码生成期间利用了 Solang 的 AST 遍历。Solang 利用这一步骤创建了控制流图(CFG)。每当我们到达一个变量声明时，我们检查符号表或契约的变量向量，以检查该声明是否应该从 CFG 中删除，如下要点所示:

同样，当我们到达一个赋值节点时，我们检查赋值左边的实体是否是一个从未使用过的变量。如果是这样，我们可以忽略这个任务，继续构建 CFG。当表达式中存在赋值时，就会出现复杂情况(例如， *a = b + (c=1)* )。Solang 已经区分了赋值语句和赋值表达式。前者是独立的赋值命令(如 *a = b+1* )，后者是表达式内部的赋值。这样，我们可以处理这种特殊情况，而不用担心独立的赋值。

如果表达式中的赋值可以被删除，我们忽略它，只把右边的赋值加到 CFG 中。右手边和它包含的表情交织在一起。下面的代码片段显示了我们在处理赋值表达式时返回给 CFG 的表达式。

值得一提的是，并不是所有的变量都可以去掉。函数参数和返回变量不应该被删除，因为它们是契约结构的重要组成部分。公共契约变量也不应该被删除，因为我们可以通过区块链事务中的访问函数直接访问它们。

Solang 已经有了一个可及的定义实现，用来补充一些特定于 Solidity 的优化。我们可以利用它来识别未定义的变量。如果一个变量已经被声明，它就是未定义的，但是没有初始化，这个未初始化的值到达一个指令，在那里变量被读取。

为了找到未定义的变量用法，我们遍历 CFG 并检查哪些定义到达了我们正在分析的指令中引用的变量。如果任何引用是未定义的，我们抛出一个错误并停止编译。

未定义变量检测和未使用变量消除利用了 Solang 现有的基础设施。我导师生涯的第三个里程碑，公共子表达式消除是一个全新的编译器阶段，所以我将在另一篇文章中介绍它。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 另外，阅读

*   [3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92) | [Pionex 评论](https://blog.coincodecap.com/pionex-review-exchange-with-crypto-trading-bot) | [Coinrule 评论](/coinmonks/coinrule-review-2021-a-beginner-friendly-crypto-trading-bot-daf0504848ba)
*   [莱杰 vs n rave](/coinmonks/ledger-vs-ngrave-zero-7e40f0c1d694)|[莱杰 nano s vs x](/coinmonks/ledger-nano-s-vs-x-battery-hardware-price-storage-59a6663fe3b0) | [币安评论](/coinmonks/binance-review-ee10d3bf3b6e)
*   [Bybit Exchange 审查](/coinmonks/bybit-exchange-review-dbd570019b71) | [Bityard 审查](https://blog.coincodecap.com/bityard-reivew) | [Jet-Bot 审查](https://blog.coincodecap.com/jet-bot-review)
*   [3 commas vs crypto hopper](/coinmonks/3commas-vs-pionex-vs-cryptohopper-best-crypto-bot-6a98d2baa203)|[赚取秘密利息](/coinmonks/earn-crypto-interest-b10b810fdda3)
*   最好的比特币[硬件钱包](/coinmonks/hardware-wallets-dfa1211730c6) | [BitBox02 回顾](/coinmonks/bitbox02-review-your-swiss-bitcoin-hardware-wallet-c36c88fff29)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)|[Hodlnaut 审核](/coinmonks/hodlnaut-review-best-way-to-hodl-is-to-earn-interest-on-your-bitcoin-6658a8c19edf) | [KuCoin 审核](https://blog.coincodecap.com/kucoin-review)
*   [Bitsgap 评审](/coinmonks/bitsgap-review-a-crypto-trading-bot-that-makes-easy-money-a5d88a336df2) | [Quadency 评审](/coinmonks/quadency-review-a-crypto-trading-automation-platform-3068eaa374e1) | [Bitbns 评审](/coinmonks/bitbns-review-38256a07e161)
*   [加密复制交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [Coinmama 审核](/coinmonks/coinmama-review-ace5641bde6e)
*   [印度的加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9) | [比特币储蓄账户](/coinmonks/bitcoin-savings-account-e65b13f92451)
*   [OKEx vs KuCoin](https://blog.coincodecap.com/okex-kucoin) | [摄氏替代品](https://blog.coincodecap.com/celsius-alternatives) | [如何购买 VeChain](https://blog.coincodecap.com/buy-vechain)
*   [币安期货交易](https://blog.coincodecap.com/binance-futures-trading)|[3 comas vs Mudrex vs eToro](https://blog.coincodecap.com/mudrex-3commas-etoro)
*   [如何购买 Monero](https://blog.coincodecap.com/buy-monero) | [IDEX 评论](https://blog.coincodecap.com/idex-review) | [BitKan 交易机器人](https://blog.coincodecap.com/bitkan-trading-bot)