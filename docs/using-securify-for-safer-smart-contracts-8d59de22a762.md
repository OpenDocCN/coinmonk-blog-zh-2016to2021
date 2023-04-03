# 使用 Securify 实现更安全的智能合同

> 原文：<https://medium.com/coinmonks/using-securify-for-safer-smart-contracts-8d59de22a762?source=collection_archive---------1----------------------->

让我们面对现实吧。撰写巧妙的合同很难。在开发智能契约时，有多个方面需要担心:所有代码都要接受公众审查，要求事务保持原子性可能会导致可重入行为，在分布式网络中很难可靠地实现随机性，等等。我们不止一次看到高调的智能合约漏洞导致了巨大的损失[【1】](https://blog.zeppelin.solutions/on-the-parity-wallet-multisig-hack-405a8c12e8f7)[【2】](/cybermiles/i-accidentally-killed-it-and-evaporated-300-million-6b975dc1f76b)[【3】](http://hackingdistributed.com/2016/07/13/reentrancy-woes/)[【4】](/@SmartBillions/smartbillions-hackathon-smart-contract-hacked-with-120-000-b62a66b34268)[【5】](https://nvd.nist.gov/vuln/detail/CVE-2018-10376)，更可怕的想法是“我们用这些损失的资金授权给谁/什么”。

为了开发更安全的智能合约，多方正在努力——开发人员教育，包括最佳实践指南和“捕捉标志”挑战，如 Ethernaut 或 Capture the Ether，更好的工具集(例如，与静态分析混合)和/或语言(Vyper ),以防止意外错误，安全扫描器等。更全面的评审可能包括由第三方使用手工评审或正式验证进行的审计，然而，可能不是每个人都可以访问的，尤其是对于那些自举或业余程序员的团队。对于有抱负的智能合同开发人员来说，一个立即可用的智能合同审查工具包是安全扫描器，如 chain security(https://securify.chainsecurity.com/)的 Securify 或 SmartDec 的 smart check(https://tool . smart dec . net/)。这些都是易于使用的在线智能合同扫描器，大多数应用程序开发人员都可以使用，两者之间的主要区别是，Securify 通过语义推理在字节码级别分析合同的安全违规，而 SmartCheck 则解析合同语言进行词汇和语法分析。出于本文的目的，我们将首先关注我们使用 Securify 的体验，包括基于 web 的安全扫描器和 Securify 后端([https://github.com/eth-sri/securify](https://github.com/eth-sri/securify))。

在我们开始之前，我想说的是，在线扫描器的工作基于试探法，无法检测出经验丰富的审计团队可以提供的预定义列表之外的漏洞，因此，在必要时，它们不应被视为广泛审计工作的替代品。永远记住，你的智能合约处理的资金越多，出现问题的风险就越高。

此外，请注意，对于启发式分析器，期望是多报而不是少报潜在的安全违规，因此会出现一些误报，然后智能合同开发人员应该努力识别攻击媒介是否存在。

从一个简单实现的契约钱包开始，它保存一个 keccak256 散列值，作为允许从该契约中检索醚的密码:

Don’t actually use this to store funds, really.

马上，我们可以看到这个钱包可能容易受到抢先运行的攻击，commitHash(至少在短时间内)可能由另一方而不是部署方初始化。让我们将它传递给 Securify，看看它得到了什么:

**Securify Web(https://securify.chainsecurity.com/)**

有了 Securify web，简单地复制粘贴上面的代码就足够了。单击“立即扫描”，稍等片刻，将会返回结果。

总共报告了 5 个问题，2 个警告。

![](img/3ee67e166a382ded54f61c209d5bb04e.png)

Issues

![](img/c3fcfed7b1576fdb4c5c248fa8b838c7.png)

Warnings

这些问题以一种相当直观的方式被报告在潜在易受攻击的代码行上，如果单击“info”按钮，则会提供进一步的详细说明和示例。

看问题:

**“交易订单影响其他金额”** —这可能是一个过度近似，因为我们在此发送合同持有的全部余额，而没有预先计算要发送的金额，因此合同将发送“全部金额”或“无”。尽管可能发生的情况是在同一个块中，给定两个有效的交易，第一个交易将提取全部金额，而第二个交易将一无所获。

在这里，我还期望出现事务排序依赖:Receiver，因为如果合同持有大量的价值，那么用明文数据监听这个地址的事务，然后用我自己的更高油价的事务先行一步，可能会变得值得。

> ***更新:在与 Securify 团队聊天后，我了解到 TOD: Receiver 不被视为 msg.sender 所描述的发送方地址是静态的。只有当智能合同中的地址是可变的，因此可能会在取款交易前更改时，这才会是一个问题。被认为是最重要的问题是 TOD: Amount，因为智能合同持有的金额可能被另一方收回，从而使合法所有者剩下 0。***

**“无限制写入存储”** —很好，Securify 捕捉到了这一点，它警告合同开发人员任何人都可以写入存储，这确实是正确的。虽然协定确实检查到只允许在存储变量 hash 没有给定值的情况下进行写入，但是攻击者可能会在很短的时间内通过插入原始用户不希望的另一个值来使协定无效。更好的方法可能是将这段代码放在构造函数下。

**“缺少输入验证”** —这是不言自明的，虽然在这个契约的上下文中不是非常重要，但是契约开发人员应该小心，特别是对于不同的数据类型。比如检查被传递给合同的地址是否意外地为 0x0，以防止资金损失 [[6]](https://github.com/ethereum/mist/issues/1583) (假设我们期待合法的交易，而不是向 burn 地址发送令牌——这可以在一个单独的函数中完成，而不需要这种检查)。

**“不受限制的以太网流量”**和**“对不受信任的合同的不安全调用”**都是合理的警告，因为合同可能会与合同或账户进行无意识的通信(例如，将以太网转移给另一方，而不是合同所有者)，因此合同开发者值得后退一步，看看这是否确实是预期的设计。

**安全本地(https://github.com/eth-sri/securify)**

既然 Securify 有 web 前端，为什么要使用 Securify 的本地实例呢？首先，可以用针对特定契约逻辑的额外安全模式来扩展 Securify(这是我正在探索的东西，也许值得单独写一篇文章)，尤其是在存在业务领域限制的情况下，这种限制是一般表达式所不能捕捉的。此外，从我玩 Securify Web 的几个星期来看，它有运行缓慢或超时的情况，公平地说，它是一项公共服务，因此有一个本地实例是有帮助的。

我使用安装了 docker 的 Ubuntu 实例部署了 Securify local，因此我们只需构建 Securify docker 映像，并将 Securify 指向我们想要分析的目录。

![](img/3a9ff91b9ac9c4e73ab6dde8a5ea4913.png)

Running Securify pointed to our directory with the .sol contract

分析完成后:

![](img/36dd9e529c6207effdcb72b3310547d2.png)

Full list of warnings and issues.

总共检测到 5 个违规(问题)和 4 个警告。此处报告的违规和警告不太详细(与提供详细说明和示例的 web 版本不同)，并且违规与 Securify Web 报告的一致。另一方面，Securify Local 似乎为我们提供了额外的警告:LockedEther、DAOConstantGas、TODReceiver 和 UnhandledException。

我认为这些被标记为警告，因为它们是部分匹配，但并不完全匹配 Securify 中规定的违规模式和安全模式，例如，LockedEther 的情况，该合同确实允许接收乙醚，但传输是从地址(this)发送的。balance 可能为 0，因此 Securify 不会将其视为违规或安全模式。这些警告充当了合同开发人员再次查看的标志，以确保这些不是真正的违规。

为了彻底起见，Securify Local 将报告的完整模式列表如下:

1.  LockedEther —报告发送给此合同的醚可能无法取回，因此被“锁定”在合同中。
2.  DAO —报告可重入攻击的可能性(类似于 DAO 的情况)，更准确地说，该模式检查是否所有 gas 都被转发。可以通过使用 transfer()或 send()操作来避免，这些操作只转发 2300 gas 津贴。
3.  DAOConstantGas —报告执行以太网传输后状态的变化可能容易受到重入攻击。例如，保存剩余醚的变量仅在发送醚后更新。在大多数情况下，应该避免使用 transfer()或 send()，但是更好的做法是利用检查-效果-交互模式 [[7]](https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html) 。
4.  tod receiver——报告由于冻结的交易排序而导致转让接收方发生变化的可能性。例如，如果预期的接收方地址被存储为存储变量，并且基于该地址执行转移，则如果在转移之前存在这样的交易，则该地址有可能被改变或重写。
5.  TODAmount —报告由于块内的交易排序而导致转账金额发生变化的可能性。类似于 TODReceiver，但在这种情况下，转移的金额可能会改变。例如，在众筹销售期间，令牌乘数*接收的令牌=要发送的令牌，其中令牌乘数是在实际发送发生之前可以改变的变量。
6.  UnhandledException —报告使用 call()或 send()时，返回值未被处理的可能性。例如，如果 send()操作发生在接收合同运行回退函数的地方，这导致了气体耗尽错误，则 send()操作不会恢复，而是返回 false。与 transfer()不同，后者的行为类似于 require(send())并会恢复整个事务。
7.  不受限制的乙醚流——报告当合同允许不加区别地接收乙醚时可能出现问题的可能性，特别是为了防止因遗漏转移操作[【8】](https://www.parity.io/security-alert-2/)而锁定乙醚的可能性。
8.  UnrestrictedWrite —报告存储变量可能被任何其他方任意覆盖的可能性，因为对它没有所有权限制。

有趣的是，如果我们浏览 Securify 的 Github([https://Github . com/eth-Sri/Securify/tree/master/src/main/Java/ch/Securify/patterns](https://github.com/eth-sri/securify/tree/master/src/main/java/ch/securify/patterns))，似乎还有几个模式，其中很多都有注释——可能还在进行中，或者仍在完善中。

请注意，Securify 不执行数值分析，因此没有溢出/下溢检查，尽管这是未来版本的计划(如 Securify 学术论文的“限制”部分所述)[【9】](https://files.sri.inf.ethz.ch/website/papers/ccs18-securify.pdf)。

**结束语**

到目前为止，我对 Securify 的体验还不错(除了 Securify Web 上偶尔出现的“坏网关”,不过我那时会继续使用我的本地实例)。真正值得称赞的是，ChainSecurity 决定在相当宽松的 Apache 2.0 许可证下开源该项目，并让 Securify 免费供公众使用，特别是当智能合同安全审计对一些个人来说可能过于昂贵时。

也就是说——启发式分析器只能发现经验中发生的事情，新的模式需要被明确地覆盖，否则可能会被遗漏。如果有必要进行更广泛的审核，那么人工审核的正式验证可能会更可靠，值得研究。

[1][https://blog . zeppelin . solutions/on-the-parity-wallet-multisig-hack-405 A8 c12 e8f 7](https://blog.zeppelin.solutions/on-the-parity-wallet-multisig-hack-405a8c12e8f7)

[2][https://medium . com/cyber miles/I-意外杀死它然后蒸发掉-3 亿-6b975dc1f76b](/cybermiles/i-accidentally-killed-it-and-evaporated-300-million-6b975dc1f76b)

[3][http://hackingdistributed.com/2016/07/13/reentrancy-woes/](http://hackingdistributed.com/2016/07/13/reentrancy-woes/)

[4][https://medium . com/@ smart billions/smart billions-hackathon-smart-contract-hacked-with-120-000-b 62 a66b 34268](/@SmartBillions/smartbillions-hackathon-smart-contract-hacked-with-120-000-b62a66b34268)

[5][https://nvd.nist.gov/vuln/detail/CVE-2018-10376](https://nvd.nist.gov/vuln/detail/CVE-2018-10376)

[6]https://github.com/ethereum/mist/issues/1583

[7][https://fra voll . github . io/solidity-patterns/checks _ effects _ interactions . html](https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html)

[https://www.parity.io/security-alert-2/](https://www.parity.io/security-alert-2/)

[9][https://files . Sri . INF . ethz . ch/website/papers/CCS 18-securify . pdf](https://files.sri.inf.ethz.ch/website/papers/ccs18-securify.pdf)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)