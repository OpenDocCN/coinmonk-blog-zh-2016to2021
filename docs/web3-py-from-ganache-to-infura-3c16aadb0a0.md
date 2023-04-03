# Web3.py:从加纳切到因弗拉

> 原文：<https://medium.com/coinmonks/web3-py-from-ganache-to-infura-3c16aadb0a0?source=collection_archive---------1----------------------->

![](img/6acabc86994dceac54db5d346bb8001a.png)

Photo by [Shahadat Rahman](https://unsplash.com/@hishahadat?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 你可以在这里找到这个教程的完整代码

Ganache 非常适合开发和测试目的，但是任何严肃的项目最终都需要转移到 testnet 中进行进一步的测试。在本文中，我将介绍我需要对代码进行的更改，以便使用像 Infura 这样的托管节点在 Ropsten testnet 上部署我的智能契约并与之交互。

您不一定需要使用 Infura 在 testnet 上部署智能契约。有些人选择运行他们自己的节点，我想象从 Ganache 到本地节点的过渡会非常顺利，唯一的代码变化是 web3 提供者。不幸的是，运行本地节点需要大量的存储、计算和带宽，因此不是每个人都愿意这样做。所以对大多数人来说，唯一的选择是使用像 Infura 这样的托管节点。然而，使用 Infura 需要对我们的代码进行一些修改。

为了说明这些变化，让我们从一个非常简单的智能契约开始，它只存储一个数字，并允许任何人更改它。契约还将发出一个事件，通知侦听器数字已经更改。

```
contract darkPool {
  uint public num; event chage(uint number); function set(uint _num) external {
    num = _num;
    emit change(num);
  }
}
```

接下来，我们将看到在使用托管节点时部署契约、发送事务和监听事件所需的更改。

我们代码中的第一个变化应该是 web3 提供者。使用 Ganache 时，我们通常会看到这样的内容:

```
w3 = Web3(HTTPProvider("[http://127.0.0.1:8545](http://127.0.0.1:8545)"))
```

使用 Infura 并没有太大的不同，我们只需要为我们的项目提供 Infura 端点，它可以在 Infura 的项目设置下找到，对于 Ropsten testnet 应该是这样的:

```
w3 = Web3(HTTPProvider("https://ropsten.infura.io/v3/<PROJECT ID>"
```

我们需要做出的下一个改变是我们部署合同的方式。使用 Ganache，我们只需调用构造函数:

```
tx_hash = contract.constructor().transact()
```

使用托管节点，我们不能这样做，因为节点不为我们管理密钥，所以我们需要做更多的工作。我们首先需要构建提供如下参数的事务，然后使用我们的私钥对其进行签名，最后将其发送到节点:

```
# build transaction
build_tx = contract.constructor().buildTransaction({
    'from': ...,
    'nonce': ...,
    'gas': ...,
    'gasPrice': ...
})
private_key = ...
# sign transaction
sign_tx = w3.eth.account.signTransaction(build_tx, private_key)
# send the transaction
tx_hash = w3.eth.sendRawTransaction(sign_tx.rawTransaction)
```

在本地执行函数(即调用)保持不变，因此对于 Ganache 和 Infura 来说，获取存储在我们的契约中的当前数字是相同的:

```
num = contract.functions.num().call()
```

另一方面，通过发送事务来执行函数又是不同的(正如我们在契约构造函数中看到的)。使用 Ganache，我们简单地写:

```
tx_hash = contract.functions.set(num).transact()
```

要使用 Infura 在 testnet 上运行同样的事情，我们可以执行以下操作:

```
num = ...
# build transaction
build_tx = contract.functions.set(num).buildTransaction({
    'from': ...,
    'nonce': ...,
    'gas': ...,
    'gasPrice': ...
})
# sign transaction
sign_tx = w3.eth.account.signTransaction(build_tx, private_key)
# send the transaction
tx_hash = w3.eth.sendRawTransaction(sign_tx.rawTransaction)
```

最后，当使用本地节点时，我们可以创建事件过滤器，然后获取所有发布的事件。使用 Ganache，我们可以执行以下操作:

```
filter = contract.events.change.createFilter(fromBlock="latest")...changes = filter.get_new_entries()
```

另一方面，我们与 Infura 的连接是无状态的，所以我们不能这样做。另一个可行的方法是:

```
start = ... # the block from which to start getting logs
changes = contract.events.change.getLogs(fromBlock=start)
```

第一次尝试在 testnet 上测试智能合约时，我花了一些时间试图弄清楚如何让我的代码工作。希望这篇文章(以及附带的代码)能帮您省去这个麻烦！