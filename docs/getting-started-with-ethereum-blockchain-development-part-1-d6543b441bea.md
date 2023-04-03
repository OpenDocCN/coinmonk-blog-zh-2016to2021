# 以太坊区块链开发入门:第 1 部分

> 原文：<https://medium.com/coinmonks/getting-started-with-ethereum-blockchain-development-part-1-d6543b441bea?source=collection_archive---------1----------------------->

![](img/52d1ede26bb40dc52f65804fb5e53d55.png)

Photo by [Thought Catalog](https://unsplash.com/@thoughtcatalog?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

**本文假设读者对区块链词汇有基本的了解:**

*   [**什么是区块链？**](https://www.coindesk.com/information/what-is-blockchain-technology/)
*   [**以太坊是什么？**](https://www.coindesk.com/information/what-is-ethereum/)
*   [**什么是 Dapp？**](https://blockgeeks.com/guides/dapps/)
*   [**什么是 EVM？**](https://www.coindesk.com/information/how-ethereum-works/)
*   [**什么是智能合约？**](https://www.coindesk.com/information/ethereum-smart-contracts-work/)

在本节中，我将尝试编写基本的智能合同，并将其部署在本地区块链上。

智能合同是用可靠的语言编写的。在编写智能合同之前，让我们为本地开发设置机器。

> [发现并回顾最佳区块链软件](https://coincodecap.com)

1.  **节点环境**:此处有[节点安装程序。您还可以使用 brew 之类的包管理器来安装节点。](https://nodejs.org/en/download/)

```
Install using Brew: ***brew install node@8***
```

2. [**Solc 编译器**](http://solidity.readthedocs.io/en/v0.4.21/installing-solidity.html) :智能合约由 *Solc 编译器编译。*安装 Solc 编译器有[种不同的方式](http://solidity.readthedocs.io/en/v0.4.21/installing-solidity.html)。您也可以使用 npm 来安装它。

```
***npm install -g solc***
```

[3。 **Go 以太坊**](https://geth.ethereum.org/) : Go 以太坊简称 geth，是运行 Go 中实现的以太坊节点的命令行接口。你可以从[这里](https://geth.ethereum.org/downloads/)安装 geth。

4. [**松露框架**](http://truffleframework.com/) **:** 松露是以太坊的开发环境和测试框架。你可以用 npm 安装松露。

```
***npm install -g truffle***
```

5.Ganache 所做的很简单，它创建了一个虚拟以太坊区块链，并生成了一些我们将在开发过程中使用的假账户。

```
***npm install -g ganache-cli***
```

**答对了！！**现在，您的本地机器已经为区块链开发做好了准备。

让我们为您的第一个区块链应用程序创建 truffle 项目。

我们可以使用 truffle 命令来初始化项目: ***truffle init***

将创建以下文件和文件夹:

*   **合同:**文件夹，用于保存实有合同
*   **迁移:**部署 solidity 契约的 JS 脚本
*   **测试:**块菌测试用例
*   **truffle.js:** Truffle 配置文件

**编写智能合同:**

1.  **添加 truffle 配置文件:**将下面的配置添加到 truffle.js 中，在下面的配置中，我们声明了开发网络，它将连接运行在 localhost 上的以太坊节点和端口 8545。

```
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*" // Match any network id
    }
  }
};
```

2.**创建合同**:让我们在合同文件夹中创建一个简单的合同‘counter . sol’文件。

反合同定义了以下内容:

我**。事件**:

**什么是可靠的事件**:事件是智能合约可以启动的调度信号。DApps，或者任何连接到以太坊 JSON-RPC API 的东西(比如 node js 应用)，都可以监听这些事件，并做出相应的动作。

反合同定义了两个事件-

*   反增量事件
*   反递减事件

二。**变量**:和其他编程语言一样，变量是保存数据的东西。

三。**方法**:计数器契约定义了递增、递减和获取计数器值的三种方法。

```
**pragma** solidity ^0.4.23;**contract** Counter {

    **event** CounterIncrementedEvent(**int** count);
    **event** CounterDecrementedEvent(**int** count); **int private** count = 0; **function** incrementCounter() **public** {
        count += 1;
        **emit** CounterIncrementedEvent(count);
    } **function** decrementCounter() **public** {
        count -= 1;
        **emit** CounterDecrementedEvent(count);
    } **function** getCount() **public constant returns** (**int**) {
        **return** count;
    }
}
```

3.**部署脚本** —为 Counter.sol 添加名为“2_initial_migration.js”的部署脚本

```
**var** Counter = artifacts.require("./Counter.sol");module.exports = **function**(deployer) {
  deployer.deploy(Counter);
};
```

4.**编写 Truffle 测试用例:**在测试文件夹中为计数器契约创建名为 counter_test.js 的测试文件。

```
**const** Counter = artifacts.require("Counter");contract('Counter test', **async** (accounts) => {
  **let** instance; before(**async** () => {
    instance = **await** Counter.deployed();//deploy contract
  }); it("Initial value of counter should be zero", **async** () => {
    **let** count = **await** instance.getCount.call({from: accounts[0]});
    assert.equal(count, 0);
  });});
```

5.**运行测试用例**

*   **运行 ganache-cli** :运行以下命令运行 ganache

***Ganache-cli***

*   **运行块菌测试**:在单独的窗口中，使用下面的命令运行块菌测试用例。

***松露测试***

耶！！！您已经部署并测试了您的第一个智能合同。

6.**添加更多测试:**

一、**增加增量计数器测试:**

```
it("Should increment counter", **async** () => {
  **await** instance.incrementCounter({from: accounts[0]});
  **let** count = **await** instance.getCount.call({from: accounts[0]});
  assert.equal(count, 1);
});
```

二。**添加测试以验证增量计数器是否发出事件:**

```
it("Should emit event on increment counter", **async** () => {
  **let** reciept = **await** instance.incrementCounter({from: accounts[0]});
  assert.equal(reciept.logs.length, 1);
  assert.equal(reciept.logs[0].args.count, 2);});
```

现在您可以尝试为减量计数器添加测试:)

Github 上有完整的源代码:[sarveshgs/first block chain app](https://github.com/sarveshgs/firstBlockchainApp)

[](https://github.com/sarveshgs/firstBlockchainApp) [## sarveshgs/firstBlockchainApp

### 这是一个简单的区块链应用程序，演示了智能合约和松露框架的用法…

github.com](https://github.com/sarveshgs/firstBlockchainApp) 

在这篇博客的第 2 部分，我将演示如何在 node js 中编写交互层，以便使用 web3 与智能合约进行交互。

[**以太坊区块链开发入门:第二部分**](http://demystifyblockchain.com/2018/06/28/getting-started-with-ethereum-blockchain-development/)

[](/coinmonks/getting-started-with-ethereum-blockchain-development-921eb42975e6) [## 以太坊区块链开发入门

### 在第一篇博客《以太坊区块链开发入门:第 1 部分》中，我演示了如何创建…

medium.com](/coinmonks/getting-started-with-ethereum-blockchain-development-921eb42975e6) 

快乐编码:)