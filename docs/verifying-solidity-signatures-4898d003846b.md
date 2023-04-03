# 验证可靠性签名

> 原文：<https://medium.com/coinmonks/verifying-solidity-signatures-4898d003846b?source=collection_archive---------0----------------------->

![](img/672c7ec70efd22c08ff21a50cf0742e4.png)

处理链上的所有东西是很棒的，但并不总是实际的，有时你可能想做一些小技巧，以节省用户的汽油。

可悲的是，简单地将所需的数据传递给合同是不安全的，如果没有安全措施，人们肯定会滥用您的合同。

这个问题有一个很好的解决方案。

# 固体签名和签名数据

您可以将所需的数据捆绑到一个散列中，并用所需的 wallet 对其进行签名。

下面你可以看到我在 hardhat 上运行一个非常简单的签名，我把签名者[0]当作我的后端。

在第 45 行，您可以看到我作为 userWallet 连接到 sigTest 契约，并启动了 isDataValid 函数。传入时间戳和签名。让我们跳到合同上看看接下来会发生什么。

查看 isdatavailable()，我们可以看到我们做的第一件事是在契约中构建散列，使用的数据与我们在客户端使用的数据相同。得到的 msgHash 应该等于客户端上的 messageHash。如果数据中有任何差异，哈希将不会匹配，签名测试将失败。

仍然看 isdatavailable()，您可以看到它触发了一个 require 并调用 isValidSignature()。在这里，我们必须首先签署散列。将**\ x19 以太坊签名的消息:\n32** 打包似乎有些武断，好好利用谷歌来理解它的重要性——从安全角度来看，这非常有趣。

有了签名的数据，我们可以利用 ECDSA 库来恢复基于散列和签名的地址。

如果恢复的地址与系统地址匹配，则一切正常，我们认为成功，否则，失败。

我不会深入研究 ECDSA 库是如何工作的，因为我相信对于开发人员来说，学习如何阅读其他合同并搜索以填补知识空白是很重要的，另外我不想破坏这种乐趣:)

[](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol) [## open zeppelin-contracts/ECD sa . sol 位于主 open zeppelin/open zeppelin-contracts

### OpenZeppelin Contracts 是一个用于安全智能合约开发的库。-open zeppelin-contracts/ECD sa . sol at master…

github.com](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol) 

# 重要注意事项

我提供的代码根本不适合生产。它写得非常快，没有经过测试。

跟踪随机数是一个很好的特性，您应该考虑添加到这段代码中。它有助于避免重放攻击。

# 参考

*   【https://solidity-by-example.org/signature/ 号
*   [https://blog . ricmoo . com/verified-messages-in-solidity-50a 94 f 82 B2 ca](https://blog.ricmoo.com/verifying-messages-in-solidity-50a94f82b2ca)
*   [https://yos.io/2018/11/16/ethereum-signatures/](https://yos.io/2018/11/16/ethereum-signatures/)

> 交易新手？试试[加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)或者[复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)