# 我觉得有趣的事情——一种特殊的智能合同蜜罐

> 原文：<https://medium.com/coinmonks/things-i-find-interesting-a-particular-sort-of-smart-contract-honeypot-1839cdad9807?source=collection_archive---------0----------------------->

最近，我在 Etherscan 上看到了一份看起来很奇怪的合同，据报道，该合同的所有者将其设置为从不知情的参与者那里窃取资金(在这种情况下，这些参与者是试图利用合同的人)。如此设置的系统被称为“蜜罐”，设置的目的是吸引攻击者，然而被监控并具有转移或利用原始攻击者的目的，这在这种情况下导致攻击者损失资金。

该合同与地址 0xe 668750 b 9 e 902 b 408d 228 FFE 41 abdb 76 c 9d 04 CFC 上的合同相似(但不完全相似):

 [## Ropsten 帐户、地址和合同

### 以太坊区块链浏览器、API 和分析平台

ropsten.etherscan.io](https://ropsten.etherscan.io/address/0xe668750b9e902b408d228ffe41abdb76c9d04cfc) 

如果我们检查代码，我们可以看到合同是一个天真地编写的合同，它充当一个有密码保护的钱包。一个人可以很容易地使用任意密码部署该合同，并且如果所有者不再能够访问原始私钥，他/她仍然可以输入正确的密码，与设置时的散列密码相匹配，并检索所有资金。

所有者似乎忽略的假设是，人们将无法看到密码，因为它是散列的，并且变量没有设置为公共的，基本上是“通过模糊实现安全性”。现在，以太坊网络上的一切都是公开的，我们当然可以在发送密码以设置钱包时取回密码，如下面的交易所示:

 [## Ropsten 事务 0 xce 6488 A8 EB 2344810738 b 312 D5 fbb 5884 e 861 EFA 6 DC 4 eff 3486 ca 66 ad 3804 a 94

### 0 xce 6488 A8 EB 2344810738 b 312 D5 fbb 5884 e 861 EFA 6 DC 4 eff 3486 ca 66 ad 3804 a 94 的 Ropsten (ETH)详细交易信息

ropsten.etherscan.io](https://ropsten.etherscan.io/tx/0xce6488a8eb2344810738b312d5fbb5884e861efa6dc4eff3486ca66ad3804a94) ![](img/53540a341207dbe2a1f65e093eb42341.png)

Getting the transaction data parameters.

十六进制的密码是 7468697369736d 7970617373776 f 72640000000000000000000(填充到 32 个字节)，简单来说就是:

![](img/ee6ec6471af0df57615f9b208c506ee6.png)

“thisismypassword” indeed.

厉害！看起来我们确实可以利用合同，试图耗尽它的余额。看起来我们确实需要用我们的事务发送 1 个 ETH，但是我们将按照下面的代码(是的，没错)来取回它:

![](img/37dbe7e33d89590d9e74ba5976d66ea2.png)

You’ll get your money back. I promise!

不幸的攻击者然后发送交易…

![](img/7972ba4fbe61262d48dca494a334f541.png)

Sending the transaction with the password.

却什么也得不到。几分钟后，出现了两个内部交易，耗尽了合同中的所有 ETH，使攻击者的 ETH 更穷。

![](img/1e830dcba0c8cf2123bd24bfa65b70e5.png)

Internal transactions indicating the contract being drained, this time, correctly.

这里到底发生了什么？该合同旨在利用以下属性来攻击攻击者:

1.  自由使用“if”语句，使交易看起来好像已经成功执行，但实际上并不满足要求的标准。另一方面，require()会抛出一个 revert，很明显密码是错误的。这使得合同看起来更加无辜，有可能欺骗多个攻击者。
2.  写得很幼稚，让攻击者误以为这是一个不知道自己在做什么的新开发人员写的糟糕代码。
3.  这是很重要的一点— **Etherscan 目前不显示发送 0 值的交叉契约调用。**因此契约可以执行状态更改，但是只要没有发送值，它就不会出现在事务列表中。

契约 0x a2 d 98d 20 EB 3496 ab B3 ab 59 D8 e 90 ad 069 CFC 30 b 59 是罪魁祸首，它最初被设置为按照上面的第 3 点执行调用。这是通过该事务完成的:

 [## Ropsten 事务 0x7f 6 ecbfa 4511d 078 EBD 21 c 02d 2 EB 83657 CB 38599086 FB 6 EAF 75d 603519 f 98662

### 0 x7f 6 ecbfa 4511d 078 EBD 21 c 02 D2 EB 83657 CB 38599086 FB 6 EAF 75d 603519 f 98662 的 Ropsten (ETH)详细交易信息

ropsten.etherscan.io](https://ropsten.etherscan.io/tx/0x7f6ecbfa4511d078ebd21c02d2eb83657cb38599086fb6eaf75d603519f98662) ![](img/26f806e0149c03cbed65ff53c1313280.png)

The real setup() call.

如果我们加载该事务的奇偶校验跟踪，我们可以很快看到该事务实际上进行了两次调用，第一次调用指向协定 0xA2D9…b59，从而产生一个内部调用，该调用调用了我们在 0xE668…cfC 上的 wallet 协定:

![](img/f85c7cec839a634a19c184794fbf2844.png)

An internal transaction call.

请注意，块号为 4516435，在块 4516438 上完成的事务 0xce 6488 ....... 804 a94 之前！

![](img/dc388d6cf8ae1f943094ebdaf0ce29d0.png)

4516435 vs 4516438

并且当设置检查*散列密码*是否已经被初始化时，密码将不是“这是我的密码”,而是攻击者已经预先设置的任何密码。

![](img/95d7b2d58e904cb34b06265bb59adae5.png)

Notice hashedpassword == 0x0

为了证实这一点，让我们检查一下存储:

![](img/a2b3118cb725fa1b2adfe7412917051f.png)

Oh my.

*hashedpassword* 不是 keccak 256(“thisismybassword”)，也不是每笔 0xce 6488……804 a94 的所有者 0x d77 e……ea8！

在这种情况下，这就是漏洞利用合同的样子。请注意，executeExploit 通过了第一个 ETH，因此出现在内部 tx 列表中，但也可能与上面类似，因为它是真正的所有者，这稍微模糊了攻击序列。

![](img/1fee326d3811fb0f9d23340566571e74.png)

Execution of exploit.

如果你有兴趣，还有更多的可以阅读:
[https://medium . com/@ Gerhard . Wagner/the-phenomenon-of-smart-contract-honey pots-755 C1 f 943 f7b](/@gerhard.wagner/the-phenomena-of-smart-contract-honeypots-755c1f943f7b)
[https://medium . com/coin monks/an-analysis-of-a-couple-ether eum-honey pot-contracts-5c 07 c 95 b 0a 8d](/coinmonks/an-analysis-of-a-couple-ethereum-honeypot-contracts-5c07c95b0a8d)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)