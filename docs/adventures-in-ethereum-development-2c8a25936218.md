# 以太坊发展中的冒险

> 原文：<https://medium.com/coinmonks/adventures-in-ethereum-development-2c8a25936218?source=collection_archive---------9----------------------->

![](img/d17d6f45443de88c2ab85e4770ada9e7.png)

You’re gonna need coffee for this

大约一年前，我提交了一篇名为“本周我从使用可靠性、松露和 Web3 中学到的 3 件事”的文章。我监控着这个空间，密切关注着一些正在进行的开发，包括从 11 月开始[为社区研究奇偶多重签名黑客做出贡献。](https://ethereum.stackexchange.com/a/30130/9955)

最近，我被邀请参加一个全公司范围的黑客马拉松，其中一些挑战涉及使用新兴技术来满足业务需求。由于软件许可管理是我直接参与的事情，我立即看到了一个用例，在这个用例中，我可以利用我在区块链开发方面的专业知识，在以太坊上提出一个基本的许可模型。

我开始研究不可替换令牌(ERC721)的开发，因为我想构建可以交易、移动、签入和签出等的许可证。这让我开始研究如何在行星间文件系统上存储与 NFT 氏症相关的元数据，这带来了另一系列独特的挑战。

最终，在所有这些原型制作之后(在黑客马拉松之前)，我认为这个项目超出了 24 小时活动的范围，我的团队转向了不同的方向。

然而，我想分享我在尝试软件许可管理的智能合同开发中发现的一些事情。尽情享受吧！

# 1.松露现在有 Ganache 和毛毛雨！

我终于有了一个本地运行的 testrpc 节点的 GUI！当我需要在调试时解析日志、块和事务时，这非常方便。然而，在几次迁移之后，实际的应用程序会变得非常缓慢；生成的日志数量似乎让应用程序不堪重负。

此外，测试套件似乎更容易利用这段时间。我实现了几乎所有契约功能的测试，这是我非常感谢 Truffle 实现的。然而，我必须实现一个定制的实用函数来断言某些事件已经触发；看起来松露仍然很难在他们的测试程序中检测到这些事件。注意:这不是我自己想出来的，它基本上是从这里抄来的:

 [## 如何在 JavaScript 测试中监听契约事件？

### 我发现不是所有的事件都显示在 truffle 输出窗口中，尽管它们可能已经触发了…

ethereum.stackexchange.com](https://ethereum.stackexchange.com/questions/15353/how-to-listen-for-contract-events-in-javascript-tests/21661) 

下面是:

此外，我能够使用毛毛雨将以太坊连接的商店集成到 React 应用程序中。它需要能够访问 Truffle projects contracts 文件夹中生成的合同 JSON 文件。此外，作为一名非 React 开发人员，我在弄清楚如何将毛毛雨连接到我的组件时有些困难。本教程对设置非常有帮助。

# 2. [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-solidity) 拥有按照 ERC 规范构建的开源合同库

因此，在尝试实现 ERC721 令牌时，OpenZeppelin 是一个巨大的资源。安装库很容易:

```
npm install zeppelin-solidity
```

然后参考/链接 ERC721 的库:

但是，在链接和开发过程中，我了解到 ERC721 令牌持有者**必须**实现*ERC 721 接收者*和**而不是***ERC 721 持有者*。我并不清楚 ERC721Holder 是一个样本契约，而不是一个可以构建的库。

此外，您可以扩展 ERC721 令牌来保存您想要的任何映射/数据。我延长了我的持牌时间。

# 3.无法存储合同代码，请检查您的汽油量

这通常意味着我还没有完全实现一个接口。在一个特殊的例子中，我意识到我没有正确地实现 *ERC721Receiver* 构造函数。

基本上:

```
constructor (address smartLicenseTokenAddress, string companyUri) public ERC721Receiver() /* <--- IMPORTANT */ { 
  _smartLicense = SmartProductLicense(smartLicenseTokenAddress);
  _companyUri = companyUri;
  _owner = msg.sender;
}
```

而不是:

```
constructor (address smartLicenseTokenAddress, string companyUri){ 
  _smartLicense = SmartProductLicense(smartLicenseTokenAddress);
  _companyUri = companyUri;
  _owner = msg.sender;
}
```

……哎呀。记住你的基本构造函数！

# 4. [IPFS](https://ipfs.io/) 用于元数据存储

还记得在链上存储数据是多么的昂贵吗？当存储 ERC721 令牌的唯一元数据时，这个问题的解决方案是使用[行星间文件系统](https://ipfs.io/)，这是一个存储不可变散列文件的分散式对等系统。文件的散列(链接)可以存储在 chain 上。这里有一个 js-ipfs-api 的链接，我用它连接到一个本地运行的 ipfs 守护进程，添加文件数据，然后检索它:

 [## ipfs/js-ipfs-api

### js-ipfs-API——IPFS HTTP API 的客户端库，用 JavaScript 实现。

github.com](https://github.com/ipfs/js-ipfs-api) 

然而，在 IPFS 存储数据变得很麻烦，因为 swarm peers 在工作时被阻止(黑客马拉松正在那里举行)，所以我不得不实现一个工作区来本地存储和检索文件。

 [## IPFS swarm peers 是“空的”，在 windows 上找不到 Webui。问题#4854 ipfs/go-ipfs

### 版本 IPFS 0.4.13 类型 bug(不确定)描述我在 Windows 10 上运行 IPFS。ipfs 守护程序正在运行。的…

github.com](https://github.com/ipfs/go-ipfs/issues/4854) 

# 5.以太坊闹钟，用于安排通话

以太坊闹钟停产了一段时间，最近才再次获得资助，这让我非常高兴，因为没有它们我无法安排执照检查。然而，我无法实现和测试预定的事务，因为这需要部署到一个测试网络，而不是本地运行的私有链(Ganache)。

然而，发现和了解在区块链协议上分散调度事务的过程就像坐过山车一样！[我强烈推荐你自己阅读这些文档。](http://ethereum-alarm-clock-service.readthedocs.io/en/latest/)

我之所以使用预定的事务，而不是懒洋洋地检查然后计算，是因为我不希望在一个将被频繁调用的调用上出现潜在的状态变化。

总之，我最终发现了一大堆非常酷的工具，可以用来在以太坊空间中构建智能合约。看到对区块链技术充满热情的开发人员在过去的一年中取得的进步真是令人难以置信！以太坊社区对这一领域开源开发的承诺确实鼓舞人心。

欢迎提问，或者查看我的 [Github](https://github.com/valkn0t) 获取更多的 Solidity 代码示例！

哦，在推特上关注我吧！