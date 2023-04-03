# 第 9 部分:用 PoW 共识算法实现区块链和加密货币

> 原文：<https://medium.com/coinmonks/part-9-implementing-blockchain-and-cryptocurrency-with-pow-consensus-algorithm-e23ded159099?source=collection_archive---------3----------------------->

在 node.js 中使用工作共识算法证明，小规模、易于理解、全面、逐步实施区块链和加密货币

![](img/ca1a2e2c4224dbbeb2b00eaca6e45a01.png)

Source: [Bitcoin Wiki](https://en.bitcoinwiki.org/wiki/Proof-of-work)

这将是这个系列的最后一部分。到目前为止，我们已经创建了我们的区块链和加密货币。剩下的就是算余额了。

基于区块链中的交易，用户可以计算余额。我们可以通过将发送给用户的事务中的所有输出相加来做到这一点。

但是这里有一个问题，如果我们把从一开始就创建的所有输出加起来，我们可能会重复计算输出。

因此，为了解决这个问题，我们将只添加自最近交易以来的输出，因为用户可能以前已经使用过这些输出。

让我们编写代码来计算 wallet 类中的余额。

每当我们创建一个新的交易时，我们都要更新余额，所以让我们将它添加到`createTransaction` 函数中。为了访问事务，我们需要一个区块链实例，所以我们将它作为参数传递。

```
createTransaction(recipient, amount,blockchain, transactionPool){ this.balance = this.calculateBalance(blockchain); if(amount > this.balance){
             console.log(`Amount: ${amount} exceeds the current
             balance: ${this.balance}`); return; } .
  .
  .}
```

我们还必须在使用`createTransaction` 时更新它。在 VS 代码中搜索`createTransaction`并更新功能。

我们将在 app/index.js 文件中传递区块链实例。

在测试文件中，我们没有区块链实例，但是我们可以创建一个，然后在需要的地方传递它。我们在 wallet/index.test.js 和 transaction-pool.test.js 中这样做

您可以重新运行测试，我们将像以前一样通过所有测试。

接下来，我们将测试`calculateBalance`函数。

将以下测试用例添加到 wallet/index.test.js 文件中。

运行测试，您将有 31 个测试通过。

我们的加密货币终于准备好了。

继续重新启动应用程序，从头开始探索它的所有功能。在不同的端口上运行几个实例，互相发送加密货币。

这就完成了项目。！！

Source: [giphy](https://media.giphy.com/media/5GoVLqeAOo6PK/giphy.gif)

# 延长

如果我们想创建一个更健壮的应用程序，您可以尝试以下方法:

1.  创建余额获取请求
2.  广播块而不是链
3.  添加事务池阈值
4.  有选择地从池中删除事务
5.  创建一个不需要被调用的自治矿工
6.  添加交易费，最快的矿工将获得交易费

如果你愿意，那么，你可以在这里阅读中本聪发表的原创论文:[原创比特币研究论文](https://bitcoin.org/bitcoin.pdf)

*你可以在这里* *找到这个项目* [*的全部工作代码。*](https://github.com/kashishkhullar/blockchain-nodejs)

感谢您的阅读。请随意提出与本文、区块链或整个世界相关的任何问题。

如果你喜欢或发现这很有帮助，请鼓掌。 ***👏***

快乐编码:)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)