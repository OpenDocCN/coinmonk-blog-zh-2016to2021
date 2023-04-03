# 第 8 部分:用 PoW 共识算法实现区块链和加密货币

> 原文：<https://medium.com/coinmonks/part-8-implementing-blockchain-and-cryptocurrency-with-pow-consensus-algorithm-74e839158f1b?source=collection_archive---------0----------------------->

在 node.js 中使用工作共识算法证明，小规模、易于理解、全面、逐步实施区块链和加密货币

![](img/ca1a2e2c4224dbbeb2b00eaca6e45a01.png)

Source: [Bitcoin Wiki](https://en.bitcoinwiki.org/wiki/Proof-of-work)

在[前一篇](/coinmonks/part-7-implementing-blockchain-and-cryptocurrency-with-pow-consensus-algorithm-bf9a16063ec1)中，我们创建事务和事务池。我们还在 p2p 服务器类中使用了事务池，并向网络广播事务。

在这一部分中，我们将创建 miner 类。

miner 类将使用我们到目前为止创建的所有功能。我们还将在这篇文章中为矿工创造采矿奖励。

但在此之前，我们需要添加一个端点。

让我们创建一个 get 请求来公开个人的公钥，我们将以 JSON 格式发送 wallets 公钥。这将允许用户看到他们自己的公钥，并决定与谁共享。

```
// get public keyapp.get('/public-key',(req,res)=>{res.json({publicKey: wallet.publicKey});})
```

您通过在`‘/public-key’`端点上发出 get 请求，在 postman 上测试它。

好了，接下来我们将把 miner 类添加到我们的项目中。在区块链中，挖掘器的主要作用是从池中提取一些事务，并执行创建块的计算工作，然后将该块存储到链中。

现在让我们问一个问题，是什么激励矿工做所有需要的计算工作来处理这些交易并把它们合并到区块链中。

答案是奖励。

我们将实施最快的矿工将获得添加块的奖励。

在实现这个奖励事务逻辑之前，让我们创建 miner 类。

向 app 目录添加一个新文件，命名为`miner.js`

在这个文件中，我们将合并到目前为止我们已经构建的所有内容。这个文件是我们项目的核心。

创建一个名为 Miner 的新类。这个类将有一个区块链，一个事务池，一个 p2p 服务器和一个钱包作为依赖。当我们在 app/index.js 中传递创建应用实例时，我们将传递这些依赖项

我们需要一个函数来开始挖掘。所以让我们添加一个叫做`mine()`的函数。

在这个挖掘函数中，我们将首先从事务池中收集所有有效的事务。

```
const validTransactions = this.transactionPool.validTransactions();
```

*我们稍后会实现* `*validTransactions()*` *函数。*

然后我们将在这个数组中添加一个奖励交易。

```
validTransactions.push(Transaction.rewardTransaction(this.wallet,Wallet.blockchainWallet()));
```

*我们稍后还将实现* `*rewardTransaction()*` *功能。*

然后，我们将使用这些事务创建一个块，对其进行签名并添加到链中。

```
const block = this.blockchain.addBlock(validTransactions);
```

创建一个块并将其添加到我们的链中后，我们将向网络广播该链。

```
this.p2pServer.syncChain();
```

当我们创建了一个块并广播它时，我们需要为新的传入事务清理池

```
this.transactionPool.clear();
```

一旦我们清除了池，我们需要告诉其他矿工实现他们的池，以防止其他矿工在他们开采的下一个块中添加旧事务。

```
this.p2pServer.broadcastClearTransactions();
```

最后，我们将返回块。

在我们继续之前，让我们在`transactionPool` 类中实现`validTransaction` 函数。在这个函数中，我们将检查输入金额是否等于输出金额的总和，还将检查签名是否有效。

接下来，让我们实现`rewardTransaction()`功能。奖励交易类似于任何其他交易，但是不同之处在于奖励交易只有一个输出。此外，交易不是由任何矿工而是由区块链本身发送的，因此我们需要为区块链创建一个单独的钱包来创建这些奖励交易。

我们将首先创建一个常数，它将是赢家矿工将收到的货币金额。让我们在 config.js 文件中添加这个常量。

```
const MINING_REWARD = 50;
```

最终 config.js 文件

现在我们将在 transaction.js 文件中创建`rewardTransaction`函数。

从`config.js`文件中导入矿工奖励常数。

在创建`rewardTransaction`函数之前，让我们对代码做一些修改，以便我们可以重用该函数。以前我们是手动添加输出，现在我们将创建一个函数来完成。

```
static transactionWithOutputs(senderWallet,outputs){
        const transaction = new this();
        transaction.outputs.push(...outputs);
        Transaction.signTransaction(transaction,senderWallet);
        return transaction;
    }
```

现在我们可以在`newTransaction`函数中使用这个函数。

```
static newTransaction(senderWallet,recipient,amount){if(amount > senderWallet.balance){
            console.log(`Amount : ${amount} exceeds the balance`);
            return;
        }
        // call to the helper function that creates and signs the
        // transaction outputs
        return Transaction.transactionWithOutputs(senderWallet,[
            {amount:  senderWallet.balance -amount,
             address: senderWallet.publicKey},
            {amount:  amount,address: recipient}
        ])
}
```

现在，让我们创建 rewardTransaction()函数

```
static rewardTransaction(minerWallet,blockchainWallet){
        return Transaction.transactionWithOutputs(
            blockchainWallet,[{
            amount: MINING_REWARD,
            address: minerWallet.publicKey
        }]);
    }
```

这里我们已经传递了一个 blockchainWallet，接下来我们将创建它。为什么我们需要一个区块链钱包？

嗯，区块链钱包是一种特殊的钱包，它会生成签名来确认和认证奖励交易。矿工自己不能成为交易的签字人。相反，区块链本身就是负责批准奖励的机构。

在 wallet/index.js 中创建函数 blockchainWallet()，该函数将生成并返回一个新的 wallet 对象。

```
static blockchainWallet(){const blockchainWallet = new this();blockchainWallet.address = 'blockchain-wallet';return blockchainWallet;}
```

继续，我们仍然需要在事务池中实现 clear 函数。

```
clear(){this.transactions = [];}
```

现在我们还将实现 broadcastClearTransaction 函数。为此，我们可以在 p2p-server 类中添加一个新的消息类型。

```
const MESSAGE_TYPE = {chain: 'CHAIN',transaction: 'TRANSACTION',clear_transactions: 'CLEAR_TRANSACTIONS'}
```

然后，我们将创建一个 broadcastClearTransaction 函数，它将这个消息发送到连接的套接字。

```
broadcastClearTransactions(){this.sockets.forEach(socket => {socket.send(JSON.stringify({type: MESSAGE_TYPE.clear_transactions}))})}
```

此外，我们必须处理这条消息，所以让我们向 messageHandler()函数添加另一个开关案例。

```
case MESSAGE_TYPE.clear_transactions:/*** clear the transactionpool*/this.transactionPool.clear();break;
```

好吧。

在我们继续之前，让我们测试我们的代码。添加以下测试案例

*transactionPool.test.js*

*transaction.test.js*

现在你将有 28 个测试用例通过。

太棒了。

继续前进

我们将创建一个挖掘端点来启动挖掘过程。

在 app/index.js 中创建一个 miner 实例。

```
const Miner = require('./miner');

  .
  .
  .const miner = new Miner(
               blockchain,
               transactionPool,
               wallet,
               p2pserver
);
```

现在我们将创建一个 post 请求`'/mine-transactions'`

```
app.get('/mine-transactions',(req,res)=>{ const block = miner.mine(); console.log(`New block added: ${block.toString()}`); res.redirect('/blocks');})
```

通过启动应用程序的几个实例来探索这个新功能。然后在每个实例之间发送几个事务，之后让其中一个实例调用这个`/mine-transaction`端点，以便从事务池中获取事务，并将其作为数据包含在区块链中。

现在，您可以检查是否每个人都收到了包含这些交易的更新区块链。通过调用`/blocks`端点。我们还可以确保实例的事务池是空的，以查看广播清除事务是否按预期工作。通过呼叫`/transctions`端点。

太好了！

在下一篇文章中，我们将在我们的加密货币中实现平衡。

*感谢您的阅读。* ***在下一部分中，我们将实现计算和更新余额的功能。*** *希望你喜欢编码。如果你发现这很有帮助，请鼓掌。*

如果你对区块链、以太坊或整个世界有任何问题，请发表评论。:)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)