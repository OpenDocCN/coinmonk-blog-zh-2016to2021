# 10 个 EVM 设计错误

> 原文：<https://medium.com/coinmonks/10-evm-design-mistakes-7c9a75a77ee9?source=collection_archive---------1----------------------->

## 以及如何不再重蹈覆辙

![](img/3ef388a19406fddf78c1dcb047b7837d.png)

以太坊区块链平台自五年前启动以来取得了令人振奋的进展。它成为了许多令人敬畏的事物的摇篮和家园，如 DAOs，ICOs，DeFi 等。

EVM(以太坊虚拟机)是以太坊生态系统的核心。它是一台执行智能合同的分布式计算机，执行代码就是法律。然而，EVM 本身也存在一些长期存在的问题，这些问题源于早期阶段的设计失误。作为一名专业的智能合同开发人员和审计人员，我一次又一次地看到这些错误的后果。

以下是我收集的 10 个 EVM 设计错误。

# 1.自毁(自杀)

EVM 有`SELFDESTRUCT`操作码(以前叫做`SUICIDE`，基本上清除了执行`SELFDESTRUCT`操作码的契约。这意味着契约的字节码和存储被删除，契约的以太网余额被转移到作为参数提供给`SELFDESTRUCT`操作码的地址。

这个想法可能是为了清除区块链州不再需要的合同。然而，它不是这样工作的，因为一个特定的契约可能只被它自己破坏，但是通常，一个契约不知道它是否仍然需要。

然而，`SELFDESTRUCT`操作码的问题不在于它没有做它应该做的事情，而是它违反了以太坊的两个重要原则:它使得契约的字节码可变(删除字节码意味着改变它),它使得在不执行契约的字节码的情况下向契约发送以太码成为可能。

还记得[平价多签钱包自毁](https://www.parity.io/a-postmortem-on-the-parity-multi-sig-library-self-destruct/)吗？人们失去了访问价值数百万美元的加密资产的权限，因为一个智能合约自毁了。

虽然不能从 EVM 中删除`SELFDESTRUCT`，但可以阻止使用它，并使 Solidity 中相应的预定义功能被弃用。

# 2.有限字宽

EVM 是一个 256 位虚拟机，这意味着它使用固定宽度的 256 位字进行操作。当运算结果不适合 256 位时，较高的位会被丢弃，这就是所谓的溢出。这是一种危险的情况，因为操作可能会产生数学上不正确的结果。

对于硬件架构，固定宽度溢出字是一个自然的决定，因为对这种字的操作可以用固定数量的逻辑门来实现，并在恒定数量的时钟周期内执行。然而，硬件架构通常提供一些方法来知道溢出是否发生，甚至获得不适合结果字的额外位。

对于低级编程语言，固定宽度溢出数据类型是一个自然的决定，因为它们以 1:1 的比例映射到底层硬件字，从而提供了最高的性能。

然而，由于现在的主流架构只有 64 位，EVM 的 256 位字不能映射到硬件字，因此无法实现为大整数，即软件模拟的任意宽度的字。

考虑到这一点，EVM 的自然决定是使用任意宽度的非溢出字，而不是固定宽度的溢出字。这将消除臭名昭著的溢出问题，并使许多事情更简单。

虽然现在不可能将体系结构从固定宽度的字更改为任意宽度的字，但仍然可以引入预编译的智能契约来实现高效的大整数操作。

# 3.堆栈太深

通常，EVM 操作码从堆栈顶部获取参数，并将结果放回那里。但是，有一些操作码，如`DUP1`、`DUP2`、…、`DUP16`和`SWAP1`、`SWAP2`、…、`SWAP16`，允许访问更深的栈元素。然而，这种随机堆栈访问仅限于 16 个最顶端的元素。

当要访问的值在堆栈中太深时，这个限制使得 Solidity 输出臭名昭著的“堆栈太深”错误。如何排列堆栈中的值取决于 Solidity 编译器，因此无法预测下次何时会出现此错误。一个常见的建议是不要使用太多的局部变量和函数参数，这很荒谬。这使得可靠性程序的正确性依赖于编译器使用的优化技术。

可以通过引入通用的`DUP`和`SWAP`操作码来解决这个问题，这两个操作码将堆栈槽深度作为正常参数。

# 4.分离的存储空间

EVM 内存被分成几个空间，通过不同的操作码访问。这些空间是:I)普通内存，通过`MLOAD`、`MSTORE`和`MSTORE8`访问；ii)堆栈，通过`DUP<n>`、`SWAP<n>`和许多其他操作码访问 iii)调用数据，通过`CALLDATALOAD`、`CALLDATASIZE`和`CALLDATACOPY`访问；iv)返回数据，通过`RETURNDATASIZE`和`RETURNDATACOPY`访问；v)字节码，通过`CODESIZE`和`CODECOPY`访问。

只要所有这些内存使用不同的操作码，在 EVM 中就不可能有一个通用的 C 风格的指针，它可以指向任何数据，不管它存储在什么样的内存中。Solidity 试图通过引入不同风格的指针来解决这个问题，如“memory”和“calldata”，但它仍然不支持“stack”、“code”或“returndata”指针。此外，这种方法不能完全解决问题，因为这些指针不能相互转换。如果一个函数接受“内存”指针，但我们需要向它传递一些数据，这些数据在代码中以文字形式存储，或者只是通过外部调用返回给我们，并且仍然驻留在返回数据中，或者从外部传递给我们的契约，并位于 calldata 中，或者只是存储在堆栈上的一个局部变量中，那么我们需要首先分配内存，并将我们的数据复制到这个区域中，这是次优的。

# 5.创建 2

最近引入了 opcode，它应该允许契约为将来的子契约保留地址，但是只在需要时才创建这些子契约。

这个想法是这样的子契约的字节码必须预先知道，以便保留一个地址，但是这些子契约甚至在被创建之前就变得不可变。不幸的是，与已经存在的`CREATE`和`SELFDESTRUCT`操作码一起，这个新的`CREATE2`操作码使得用任意新的字节码替换已部署契约的字节码成为可能，同时保留契约的地址。这打破了合同不变性的以太坊原则。

在某些情况下，[在契约的字节码](/coinmonks/on-efficient-ethereum-storage-c76869591add)中存储和更新数据甚至比在契约的存储器中更便宜。

# 6.无声算术错误

这里的算术错误是指运算产生数学上不正确的结果的情况，即溢出、下溢和被零除，在 EVM 中返回零。

主流硬件架构提供了一些方法来知道操作是否返回数学上正确的结果，但是 EVM 没有提供这样的功能。这使得安全地进行数学运算变得困难且昂贵。

这个问题可以通过引入新的操作码来解决，该操作码将获得最后执行的操作码的状态标志。

# 7.不可变字节代码

合同的字节码是不可变的。这是以太坊的核心原则之一，可惜被`SELFDESTRUCT`操作码打破，更被`CREATE2` + `CREATE` + `SELFDESTRUCT`操作码组合打破。但是至少对于不使用`SELFDESTRUCT`的合同，存储在链上的字节码是不可变的。这意味着人们可以在与契约交互之前研究字节码，并确保代码在被研究和被交互之间不会改变。

然而，在某些情况下，在运行时修改内存中自己的字节代码，而不将这些修改保存在链上，对协定来说是很方便的。这种修改不会破坏不变性原则，因为存储在区块链的代码将保持不变，但将使合同有可能修改其代码，甚至根据输入动态生成新代码，从而降低天然气成本。

目前，EVN 禁止这种做法，但仍有可能通过新的操作码来实现这一点。

# 8.缺乏可扩展性

向 ENV 添加新功能有两种常见方式:引入新的操作码和引入新的预编译契约。对于每一种特殊情况使用什么方法没有严格的规定。Keccak256 哈希实现为操作码(`SHA3`)，而 SHA256 和 HIP160 哈希函数实现为预编译契约。这两种方式都需要硬分叉，并且这两种方式都不是 100%向后兼容的，因为它们可能会影响已经部署的契约的行为。

此外，纯粹在 EMV 内操作的低级操作码(即，仅影响堆栈、存储器和指令指针)与处理区块链状态的高级操作码混杂在一起。

EVM 应该有一些适当的方式来添加新功能，如用于执行系统调用的特殊`SYS`操作码，即调用一些外部功能。它还应该清楚地分离独立于区块链状态结构的低级操作码和与该状态交互的高级系统调用。

# 9.过于宽泛的词语

虽然有限的单词宽度问题已经提到，但另一个问题是这个宽度限制……太宽了。256 位字比主流硬件本身支持的 64 位字宽得多。这使得使用这种字的操作代价很高，即使实际值很小。处理布尔值、字符、数组索引和其他小数字会消耗大量的气体:也就是说，与数字很大时消耗的气体一样多。

解决这个问题的一个方法是让操作码在参数的低 64 位甚至 32 位上操作。

# 10.无法访问其他合同的存储

在以太坊中，契约的存储是公共信息，而对于其他契约则不是。当一个契约没有为保存在契约存储中的一些有价值的信息提供 getter 函数时，人们仍然可以离线读取这些信息。只需要算出存储槽地址。但其他合同不能做到这一点。目前，契约作者试图将几乎每个存储变量都声明为公共的，以防在其他契约中需要它的值，因为在契约部署后不可能将变量公开。

通过引入从另一个契约的存储中读取的`EXTSLOAD`操作码，可以很容易地解决这个问题。

上面描述的问题是我收集了大约 4 年的开发和审计以太坊智能合同。这些问题不是致命的，可以解决，但是，大多数问题可以很容易地解决，至少其中一些问题肯定值得解决。

如果你知道一些应该添加到这个列表中的东西，请告诉我。

## 另外，阅读

*   最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [德里比特审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和测试网
*   [FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   [Bybit 交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [莱杰纳米 S vs 特雷佐 one vs 特雷佐 T vs 莱杰纳米 X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   [Bitsgap 评论](/coinmonks/bitsgap-review-a-crypto-trading-bot-that-makes-easy-money-a5d88a336df2)——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   [HaasOnline 评论](/coinmonks/haasonline-review-d8d1a3400419)享受九折优惠
*   [埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)
*   [SecuX Stone 评论](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)
*   [BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |从您的密码中赚取高达 8.6%的利息
*   [开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)

> [直接在您的收件箱中获得最佳软件交易](/coinmonks/newsletters/coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://medium.com/coinmonks/newsletters/coinmonks)