# 用 Go 对以太坊上的可靠性契约进行单元测试

> 原文：<https://medium.com/coinmonks/unit-testing-solidity-contracts-on-ethereum-with-go-3cc924091281?source=collection_archive---------1----------------------->

## 以太坊区块链上的智能合约可以用 Solidity 语言编写。有几种工具和框架可用于测试这些智能合约。本文演示了如何在不牺牲并发性的情况下，以类型安全的方式对这些契约进行单元测试。

![](img/6a5b33cfcab6a21e0fb53cebcfd13499.png)

我的朋友最近向我介绍了使用 [Solidity](https://github.com/ethereum/solidity) 编程语言为[以太坊](https://github.com/ethereum)区块链编写用于去中心化应用的[智能契约](https://en.wikipedia.org/wiki/Smart_contract)(或简称为 [dapps](https://blockgeeks.com/guides/dapps/) )。他向我提到了他一直在用 [Truffle](https://github.com/trufflesuite/truffle) 框架开发的一个项目。从我所听到的和看到的来看，这是一个非常受欢迎的工具，它由一系列相关工具组成，这些工具有着令人愉快的名字，如 [Ganache](http://truffleframework.com/ganache/) 和[毛毛雨](http://truffleframework.com/docs/drizzle/getting-started)。除了一旦我开始真正咬一口，它们不会留下令人愉快的回味。[其他测试框架](https://ethereum.stackexchange.com/questions/607/how-to-unit-test-smart-contracts/649#649)如 [Embark](https://iurimatias.github.io/embark-framework/) 、 [dapple](https://github.com/NexusDevelopment/dapple) 和 [Populus](http://populus.readthedocs.org/en/latest/) 也存在于不同的图书馆生态系统中。但是都是用 [Python](/@natemurthy/all-the-things-i-hate-about-python-5c5ff5fda95e) 或者 JavaScript 写的。

我来自一个 [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) / [Scala](https://github.com/scala/scala) 背景，最近一直在为 [Tesla](http://www.tesla.com/) 的软件开发大量用 [Go](https://github.com/golang/go) 编程语言编写的服务。所以类型安全和并发对我来说很重要。出于这些原因，Python 和 JavaScript 是我最不喜欢用来编写和维护可伸缩代码的编程语言。在写这篇文章的几个月前，我花了一些时间浏览区块链实现的源代码库，如比特币、莱特币、以太坊、Ripple 和 Monero。在我真正看过的所有代码中， [go-ethereum](https://github.com/ethereum/go-ethereum) 的实现对我来说就像一颗闪亮的星星。代码易于阅读，易于构建，并且有很好的文档记录。它实际上是*领先以太坊的*实现。我在网上找到的许多测试 Solidity 契约的例子都是基于以太坊 JSON-RPC 规范的 [web3.js](https://github.com/ethereum/web3.js/) 工具包，这对于开始使用`geth console`来说是很棒的，但是我想要更简单的东西。我想要的是直接与`geth`进行进程间交互的东西，而不是包裹在各种各样的软毛层中的东西，或者需要我针对一个活动节点测试契约的东西。

# 围棋以太坊中的区块链模拟器

我去搜索用 Go 编写的 Solidity 单元测试库，很高兴地发现了一个关于以太坊契约的 Go 绑定的 wiki 页面。在这篇报道中，我特别激动地发现`go-ethereum`配备了一个[区块链模拟器](https://github.com/ethereum/go-ethereum/wiki/Native-DApps:-Go-bindings-to-Ethereum-contracts#blockchain-simulator)。让我重复一下: ***以太坊飞船的 Go 实现带有区块链模拟器！“那是个相当糟糕的屁股，”我心想。***

但是当我一行一行地遵循 wiki 中的示例时，我有点沮丧地遇到了与我从 Github 克隆的 go-ethereum 源代码版本(`master@d2fe83d`)不兼容的代码片段。现在，如果我对 wiki 示例进行故障排除的细节让您感到厌烦，请随意跳到下一节，只要知道我最终让它们工作了:)但是对于更好奇的读者来说，破坏我的构建的特定代码是:

```
sim := backends.NewSimulatedBackend(
        core.GenesisAccount{
                Address: auth.From,
                Balance: big.NewInt(10000000000),
        })
```

这会导致编译错误:

```
cannot use core.GenesisAccount literal (type core.GenesisAccount) as type core.GenesisAlloc in argument to backends.NewSimulatedBackend
```

一定喜欢编译器，对吧？好了，这个版本的问题是 genesis 处理在 [geth 1.6.0](https://github.com/ethereum/go-ethereum/commit/37dd9086ec491900311fc39837f4a62ef5fd3a4a) 中被重构了，上面来自 [@karalabe](https://github.com/karalabe) 的文章是在那个版本发布前几个月创作的。`NewSimulatedBackend`构造函数方法的类型签名现在看起来像这样:

```
**type** GenesisAlloc **map**[common.Address]GenesisAccount**func** NewSimulatedBackend(alloc core.GenesisAlloc) *SimulatedBackend
```

这意味着从 [1.6.0](https://github.com/ethereum/go-ethereum/releases/tag/v1.6.0) 开始，创建一个模拟以太坊区块链的正确方法是:

```
gAlloc := **map**[common.Address]core.GenesisAccount{
        auth.From: {Balance: big.NewInt(10000000000)},
}sim := backends.NewSimulatedBackend(gAlloc)
```

我喜欢这种推理。想象一下，尝试用 Ruby、Python 或 JavaScript 解决这些问题。不是说做不到，只是不好看。好了，这样就可以使用上面链接的 wiki 页面中提供的绑定了，这让我开始使用这个包。现在让我们用一个更简单、更新的例子来编写一些测试。

# 单元测试:Hello World

首先，我鼓励每个软件开发人员学习一些围棋。几年前，我不愿意学习另一种语言，尤其是在对 Java 和 Scala 投入了大量资金之后，那时我最初只是把它当作谷歌规定的一种时尚。但是随着我阅读和编写 Go 的越来越多，我对用 Go 编写的软件的质量、成功的 Go 项目的复杂性(例如 [Docker](https://github.com/docker) 、 [Kubernetes](https://github.com/kubernetes/kubernetes) 等)印象越来越深刻。)，Go 开发者的社区，它的采用率，它的库的多样性，它的使用的简单性。我们将下载、安装和构建以下依赖项，作为 Hello World 智能合约的先决条件:

*   去
*   去以太坊
*   `[abigen](https://github.com/ethereum/go-ethereum/tree/master/cmd/abigen)`
*   `[solc](http://solidity.readthedocs.io/en/latest/installing-solidity.html)`

如果这是你第一次使用 Go，你可以从 [golang“入门”页面](https://golang.org/doc/install)下载并安装任何最新的发行版。我下面准备的例子适用于 Go 1.9.2。一旦你安装了 Go，克隆 go-ethereum 源代码并签出最新的标签(在撰写本文时是`v1.8.7`)，这样我们就可以构建最新的稳定版本了:

```
$ **cd** $GOPATH/src/github.com/ethereum
$ **git** clone [https://github.com/ethereum/go-ethereum.git](https://github.com/ethereum/go-ethereum.git) 
$ **cd** go-ethereum
$ **git** checkout v1.8.7
```

接下来，我们要安装必要的工具来编译我们的 Solidity 文件。大多数已经在使用 Solidity 的人将会熟悉 web 服务，比如用于从`*.sol`文件编译和生成以太坊绑定的 [Remix](https://remix.ethereum.org/) 或 [SolC API](https://solc-docs.smartcontract.com/) 。为了编写我们的单元测试，我们希望在本地编译和生成这些绑定。为此，我们将从 go-ethereum 的源文件夹中安装附带的`abigen`工具:

```
$ **go** install ./cmd/abigen
```

还有`solc`工具，你可以按照[solidity . readthedocs . io](http://solidity.readthedocs.io/en/latest/installing-solidity.html)上的说明来做——不过如果你是在 Mac OS X 上开发，而不是在 NPM 上，我建议你用自制软件安装 Solidity 编译器:

```
$ **brew** tap ethereum/ethereum
$ **brew** install solidity
```

现在，在您选择的工作目录中，让我们将“Hello World”智能合约写入一个名为`helloworld.sol`的文件:

```
**pragma** solidity ^0.4.23;**contract** helloworld { **function** say() **public** **pure** **returns** (string) {
        **return** 'hello etherworld';
    }}
```

接下来，我们将使用以下命令为这个简单的契约生成 Go 绑定:

```
$ **abigen** --sol helloworld.sol --pkg main --out helloworld.go
```

`abigen`工具不仅会为你的 Solidity 契约生成本地 Go 绑定，它还会调用`solc`命令来生成一个应用二进制接口( [ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) )字符串(在`helloworld.go`中标记为常量`HelloworldABI`)以及将在我们模拟的以太坊虚拟机中运行的字节码(标记为`HelloworldBin`)。我已经将[要点](https://gist.github.com/natemurthy/50415bdac7bd5a1194c39c10aa4a7c94)附加到我用上面的命令代码生成的输出文件中，以供参考，但是我建议您自己尝试 codegen 来验证您的工具在您的本地开发机器上设置正确。有了这个 Go 绑定，我们现在可以为我们的`Say()`单元编写一个测试，它只需要这个函数的输出字符串`"hello etherworld"`:

现在让我们继续运行我们新创建的智能契约单元测试，使用:

```
$ **go** test -v helloworld*.go=== RUN   TestRunHelloworldSuite
=== RUN   TestRunHelloworldSuite/TestSay
--- PASS: TestRunHelloworldSuite (0.00s)
--- PASS: TestRunHelloworldSuite/TestSay (0.00s)
PASS
ok   command-line-arguments 0.041s
```

然后*瞧！*我们所有的智能合约单元测试都通过了，无需运行或配置专用网络，也无需下载和安装任何附加软件。

在上面的测试代码中，我使用了一个名为`[testify](https://github.com/stretchr/testify)`的 Go 包，我发现它非常有助于将大量相似的单元测试组织成套件——例如，每个契约都可以有一个为其编写测试的套件。单元间重用的所有测试状态都封装在`HelloworldTestSuite`中，然后在`SetupTest`中连接起来，用区块链模拟器进行配置。首先，我们用`crypto.GenerateKey()`创建一个私钥，它将被用来填充一个 signer 函数，以便在我们的模拟以太坊后端中授权交易。函数`bind.NewKeyedTransactor`返回一个`*TransactOpts`，它指向一个我们可以进行交易的地址。我们将使用这个地址创建一个带有资金分配帐户的 genesis 块，然后通过前面描述的重构的 genesis 处理机制用这个帐户创建我们的模拟区块链。然后，我们使用`DeployHelloworld`部署我们的“hello world”契约，其中`s.auth`指向从生成的帐户进行交易所需的所有授权数据，而`s.sim`指向由内存数据库支持的完全模拟的区块链。部署该契约的事务保持在“待定”状态，直到我们将它`s.sim.Commit()`给区块链，这使得契约代码在给定的地址可用。最后，我们可以调用并测试我们的`s.helloworld.Say`函数的输出，这个函数可以用非零契约调用者选项进行微调。

现在我们有了:一份用 Solidity 编写的智能合同，在 Go 中经过测试，无需启动节点或连接到网络。

***鸣谢*** *:特别感谢我的朋友* [*乔纳森·维雷*](/@jfviray7) *总是提供新鲜的想法。*

[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)