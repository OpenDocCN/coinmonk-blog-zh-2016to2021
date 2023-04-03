# 正式智能合同验证

> 原文：<https://medium.com/coinmonks/formal-smart-contract-verification-4b19cf848ff9?source=collection_archive---------1----------------------->

## 由[运行时验证公司](http://www.runtimeverification.com)

运行时验证公司提供[正式智能合同验证](https://runtimeverification.com/smartcontract)服务。

如果您仍在决定是审核还是正式验证您的智能合同，请知道我们提供了最先进的正式保证。本文解释了原因和方法。

**综合**。我们指定并验证智能合约的全部功能正确性。这为智能契约所有者提供了最强的功能正确性保证。

**端到端**。我们从智能合约的高级规范开始，并将其细化到虚拟机级别。最后一步是验证可执行字节码。

**忠实**。我们与智能合约所有者沟通，以确认高级规范正确地捕捉了预期的功能。然后我们证明了规范精化的合理性。我们将很快生成正确性证书作为正式的证明对象，由独立的证明检查器进行检查。

**知未知**。我们检测编译器隐藏的弱点和意想不到的行为，如果放任不管，可能会导致安全漏洞和利用。简而言之，我们不信任编译器，而是验证它们生成的字节码。

# 我们的方法

为了验证智能契约，我们首先将其形式化为数学规范。这通常需要与智能合同所有者进行几轮讨论和会议。代码改进的机会通常是在这个早期阶段发现的。

接下来，我们细化规范以匹配目标低级虚拟机(VM)，例如以太坊虚拟机(EVM)或 IELE。然后，我们将智能合约从其高级语言(例如，Solidity、Vyper、普路托斯)编译成 VM 字节码(例如，EVM、IELE)。只有这样，我们才能证明最终的字节码满足细化的规范。

我们使用与 UIUC 合作创建的 [K 框架](https://runtimeverification.com/k/)，它包括一个自动从目标语言或 VM 的形式语义中导出的构造正确的程序验证器；例如，参见 [KEVM](https://runtimeverification.com/blog/lets-make-the-ethereum-virtual-machine-better/) ，我们的形式语义 [EVM](https://www.ethereum.org/) ，或者 [IELE](https://runtimeverification.com/blog/iele-a-new-virtual-machine-for-the-blockchain/) ，一个为 [Cardano](https://www.cardano.org) 开发的新 VM。展望未来，我们计划支持 [EVM-WASM](https://github.com/kframework/wasm-semantics) 。

# 为什么要正式验证

传统的审计员可能会建议改进代码质量或可读性的变更，但是不能提供关于代码功能正确性的数学上严格的工件。相对于市场上的非正式解决方案，我们可以提供明显的优势。轻量级静态分析工具可能会发现一些已知的错误模式，但也可能会返回很高的误报率。最终，这样的工具缺乏为您的程序定制的规范所能提供的全面覆盖。

# 流程如何运作

该过程有五个主要步骤:

**形式化**

我们基于所有者提供的典型的非正式规范，形式化所有者智能契约的高级业务逻辑。我们为智能合约的预期功能以及它必须满足的重要属性创建精确、定制和全面的规范。

**确认**

这个高级规范需要由智能契约的所有者确认，可能需要经过几轮讨论和更改，以确保它正确地捕获了他们的契约的预期行为。在这个早期阶段，甚至在我们开始验证过程之前，我们经常会发现智能合约中的错误。

**细化**

然后，我们将规范一直细化到虚拟机级别，通常分多个步骤，以捕获特定于虚拟机的详细信息。最终 VM 级规范的作用是确保在字节码级不会发生任何意外，也就是说，当字节码被执行时，只有在高级规范中指定的事情才会发生。

**编译&测试**

然后，我们使用将用于部署契约的相同编译器版本，将智能契约从其高级代码编译成最终的 VM 低级代码。在这个阶段，我们还使用智能合同所有者提供的任何测试，以及我们可能开发的用于增加代码和规范覆盖率的额外测试，针对最终的字节码测试细化的规范。

**验证**

最后，我们根据 VM 级规范正式验证智能契约的编译后的 VM 字节码。因此，我们不依赖编译器的正确性。为了严格推理 VM 字节码而不遗漏任何 VM 异常，我们使用了 [K-framework](https://runtimeverification.com/k/) 的构造正确演绎程序验证器，它将 VM 的形式语义作为输入。

# 接触

从[运行时验证公司](https://runtimeverification.com/smartcontract/)了解更多信息，请访问我们的 [GitHub 页面](https://github.com/runtimeverification/verified-smart-contracts)。请将您的问题发送给我们。

# 相关故事

[](http://runtimeverification.com/blog/how-formal-verification-of-smart-contracts-works/) [## 智能合同的正式验证是如何工作的

### Brian Marick 和 DAE jun Park Runtime Verification Inc 提供正式的智能合同验证服务。在这个…

runtimeverification.com](http://runtimeverification.com/blog/how-formal-verification-of-smart-contracts-works/)