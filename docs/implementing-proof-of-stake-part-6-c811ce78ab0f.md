# 实施利害关系证明第 6 部分

> 原文：<https://medium.com/coinmonks/implementing-proof-of-stake-part-6-c811ce78ab0f?source=collection_archive---------0----------------------->

## 领导者、区块和 ICO

![](img/67a3c3a562cf394e00d1dc1fd2774c83.png)

在前一篇文章中，我们创建了账户、股权模型和验证器。在本文中，我们将为我们的应用程序创建一个 ICO 和一个静态 leader。我们还为我们的块添加了一个阈值，这将限制它的大小，并告诉我们何时应该确定下一个领导者。

# 交易阈值

在此应用中，阈值被定义为单个块中可以添加的事务数量。

创建一个常量 TRANSACTION_THRESHOLD，用作块的大小，并将其添加到`config.js`文件中

```
// config.js
const TRANSACTION_THRESHOLD = 5;const TRANSACTION_FEE = 1;module.exports = {
  TRANSACTION_THRESHOLD,
  TRANSACTION_FEE
};
```

我们的门槛只能达到一个地方，即 p2p-server。这是我们接收新交易并将其添加到池中的地方。因此，在 p2p 服务器中，我们将添加一些逻辑来检查是否达到了阈值。

我们可以编写一些代码来检查事务消息处理程序中的池长度。

```
// p2p-server.js
.
.
.case MESSAGE_TYPE.transaction:
 if (!this.transactionPool.transactionExists(data.transaction)) { // check if pool is filled let thresholdReached = this.transactionPool.addTransaction(
      data.transaction
    ); this.broadcastTransaction(data.transaction);
  }
  break;
.
.
.
```

必须修改`addTransaction`函数，以便在达到阈值时返回一个`true`值，否则返回`false`值。从`config.js`文件导入常量，并在添加新事务时检查池的长度。

```
// transaction-pool.jsaddTransaction(transaction) {
  this.transactions.push(transaction);
  if (this.transactions.length >= TRANSACTION_THRESHOLD) {
    return true;
  } else {
    return false;
  }
}
```

# 领导者

下注最多硬币的验证者将成为领先者。让我们编写代码来获取给定块的领导者。

在`blockchain.js`文件中，我们可以添加调用 stake 类的`getMax`的函数`getLeader`。

```
// blockchain.jsgetLeader() {
  return this.stakes.getMax(this.validators.list);
}
```

`getLeader`返回下注硬币最多的节点的地址。`this.validators.list`是已经支付验证者费用并有资格被选为领导者的节点的地址列表。

我们将在 p2p 服务器中使用这个函数来检查当前节点是否是领导者。

*注意:我们需要为上面的代码片段准备一个钱包。我们可以传递 wallet 实例，同时在* `*index.js*` *文件中创建一个 p2p-server 作为依赖项。*

最后，如果领导者当选，他现在可以创建一个块，并将该块广播到网络。在向网络广播之前，一个领导者在块上签名。因此，我们需要更新 createBlock 函数，并将钱包作为参数传递。

首先进入`blockchain.js`

```
// blockchain.jscreateBlock(transactions, wallet) {
  const block = Block.createBlock(
    this.chain[this.chain.length - 1],
    transactions,
    wallet
  );
  return block;
}
```

而在 block.js 中

```
// block.jsstatic createBlock(lastBlock, data, wallet) {
  let hash;
  let timestamp = Date.now();
  const lastHash = lastBlock.hash;
  hash = Block.hash(timestamp, lastHash, data);

  // get the validators public key
  let validator = wallet.getPublicKey();

  // Sign the block
  let signature = Block.signBlockHash(hash, wallet);
  return new this(timestamp, lastHash, hash, data, validator, signature);
}
```

现在，我们可以在 p2p 服务器中使用它来创建块并广播它。

让我们实现`broadcastBlock`功能。它只需要一个参数，即要广播的块和一个`sendBlock`函数，就可以将它单独发送到每个连接的套接字，类似于我们对事务所做的。此外，我们需要为此添加一个新的`MESSAGE_TYPE` 和一个相应的消息处理程序。

```
const MESSAGE_TYPE = { chain: "CHAIN", block: "BLOCK", transaction: "TRANSACTION"};
```

广播块

块的消息处理程序: *(* `*isValidBlocks*` *稍后实现)*

# 块的有效性

接收到的块可能是无效的、损坏的或旧的，因此诚实的节点应该只广播经过认证的块。要检查块的有效性，让我们在区块链类中编写一个函数，检查块是否可信，是否应该添加到链中。

无效块的可能原因—

1.  无效哈希
2.  lastHash 无效
3.  无效的领导
4.  无效签名

下面将所有情况总结成一个函数。

现在，领导者可以创建块并广播它。当一个节点接收到一个有效的块时，它必须执行该块中的所有事务以具有最新的状态。

让我们为这个作业在区块链类中创建一个 executeTransactions 函数。

对于每种类型的事务，处理方式是不同的。我还在`accounts.js`中添加了一个`transferFee` 函数，它将费用从发送者发送给街区的领导者。

# 清除池

执行完块后，我们必须清空池，从池中删除不需要的事务。

```
// transaction-pool.jsclear() { this.transactions = [];}
```

这个函数应该在我们添加并执行了这个块之后被调用。

# 图标

我们的应用程序几乎完成了，还有一件事要做。首次发行硬币。

在最初的一段时间内，没有 ICO 或工作证明算法，赌注证明系统不能工作，直到开采出足够的硬币。

可以简单地说，我们将需要一个最初的领导者，他将持有所有的硬币，并将它们转移到其他节点。最初的领导者必须硬编码到类、映射和列表中，并且应该有足够的初始平衡和赌注。

为此，我们将创建一个单独的应用程序，专门负责初始硬币发行。稍后，我们可以断开该节点与网络的连接。

在根目录下创建一个名为`ico`的文件夹。在这个文件夹中，创建一个文件`index.js`。更新`package.json`并添加运行 ICO 应用程序的脚本。

```
"scripts": {"dev": "nodemon ./app","ico": "nodemon ./ico",},
```

`ico/index.js`几乎与`app/index.js`相似，但有一个已知的钱包。

请注意，wallet 实例有一个秘密“我是领导者”。我们可以很容易地找到这个秘密对应的公钥。

在`wallet.js`文件所在的同一个目录中运行一个节点控制台，并为密钥创建一个 wallet 类的对象。使用这个对象将找到领导者的公钥。

```
> let Wallet = require('./wallet');
> let leader = new Wallet('i am the first leader');
> leader.getPublicKey();'5aad9b5e21f63955e8840e8b954926c60e0e2d906fdbc0ce1e3afe249a67f614'
```

这是领导者的公钥或地址，并且将保持不变，因为秘密是相同的。

现在我们可以将这个地址添加到我们的代码中，并给 leader 一个初始平衡，添加 stake 并使它成为一个验证器。

帐户. js

```
class Account {
  constructor() {
    this.addresses = [
      "5aad9b5e21f63955e8840e8b954926c60e0e2d906fdbc0ce1e3afe249a67f614"
    ];
    this.balance = {
      "5aad9b5e21f63955e8840e8b954926c60e0e2d906fdbc0ce1e3afe249a67f614": 1000
    };
  }
  .
  .
  .
  .
```

stake.js

```
class Stake {
  constructor() {
    this.addresses = [
      "5aad9b5e21f63955e8840e8b954926c60e0e2d906fdbc0ce1e3afe249a67f614"
    ];
    this.balance = {
      "5aad9b5e21f63955e8840e8b954926c60e0e2d906fdbc0ce1e3afe249a67f614": 0
    };
  }
  .
  .
  .
  .
```

最初零股份就足够了，因为第一个领导者将是列表中的唯一领导者。

验证器. js

```
class Validators {
  constructor() {
    this.list = [
      "5aad9b5e21f63955e8840e8b954926c60e0e2d906fdbc0ce1e3afe249a67f614"
    ];
  }
  .
  .
  .
  .
```

现在第一个领导者可以向其他节点发送硬币并引导系统。

一旦节点有了足够的平衡，它们就可以成为验证器，并添加一个桩来锻造块。

运行一个 ICO 应用程序和其余的正常应用程序。使用 ICO APIs 将硬币转移到其他节点。您可以从 API 中获得公钥。让其他节点发送验证费和赌注一些硬币。下一个块将由新的验证器生成。

这最终完成的是股权实施的证明。我已经在 [github](https://github.com/kashishkhullar/pos-blockchain) 上添加了完整的源代码供你参考。

**扩展:**

1.  对于一段时间后加入网络的新节点，创建`executeChain` 函数
2.  惩罚任何篡改块的欺诈节点
3.  创建`isValidTransaction` 函数来验证交易
4.  添加连接关闭处理程序，以平稳地从网络中删除节点，而不会使应用程序崩溃。

*感谢您阅读* ***。我们成功地完成了我们的项目。*** *希望你喜欢编码。如果你发现这很有帮助，请鼓掌。*

如果你对区块链、以太坊或整个世界有任何疑问，请发表评论。:)

[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)