# 一个简单的 ERC20 兑换钱包

> 原文：<https://medium.com/coinmonks/a-simple-erc20-exchange-wallet-c6720e6c8005?source=collection_archive---------0----------------------->

## 为 ERC20 代币制作一个保管钱包并不像你想象的那么简单

![](img/bc6c4b89d8016d371e49d5147d49f806.png)

## 更新

我是一个很棒的项目的技术顾问。我们正在交易所建设下一代，如果可以的话，我鼓励你去看看！

## 放弃

*请明智一点，在没有进行尽职调查并确保代码满足您的安全需求之前，不要将本文中的代码投入生产。本文仅供娱乐和教育之用，并不构成对您组织的安全或保管决策的建议。*

## 问题是

以太坊钱包在交易所管理起来可能有点棘手，因为每个以太坊地址都有自己的钱包。它不像比特币或 Cardano，你可以在主地址下生成子账户，反映余额，并用单个钱包地址进行管理。这在交换的典型流程中提出了一个问题，其中用户将生成一个存款地址来发送他们的资金。在以太坊中，如果我们为用户生成一个帐户来发送他们的资金，我们还需要在交易所中维护该帐户的私钥。这是一场监护权的噩梦，无法维持。

> 还可阅读:[最佳加密货币硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)

## 我们想要什么

我们想要做的是在我们的交易所有一个保管钱包，我们有一个**单个私钥**并生成**多个** **公共存款地址**供客户存款。

## 我们的解决方案

解决这个问题的一个方法是创建一个*智能契约*，它创建并拥有其他*智能契约*。这是非常高的水平，但是我们本质上是这样做的

*   部署由我们的**保管以太坊账户**拥有的智能合同，该合同能够创建**子钱包**并返回它们的列表
*   让子钱包由**保管以太坊账户**和**而不是**由父智能合约拥有

这将允许**保管账户**管理发送到该钱包的所有资金。这是一种类似的方法，大型托管提供商如 ***、BitGo*** 都使用这种方法。

# 超级钱包！！！

我们的 *MegaWallet* 代码如下，你可以看到我们创建钱包的方法，并列出拥有的钱包。

如果您注意到，我们能够在创建的 *ERC20Wallet* 中存储**任何** *ERC20* 令牌—只要它符合**open zeppelin**detailed er C20 类型。

```
ERC20Wallet wallet = new ERC20Wallet(DetailedERC20(_token), owner);
```

这既强大又危险，因为你可以在一个钱包里存放你在交易所的所有代币。你不应该这样做，但你应该创造**多个** **热**和**冷**钱包，在流动性和**安全性**之间保持良好的平衡。

## ERC 20 钱包

这就是我们将**生成**并将**公共地址**发送给用户存放的子钱包。

注意，我们传入了**令牌**地址和**所有者**地址。我们传入**令牌**地址，这样当我们想要**清除**所有存款账户时，我们就可以转移那个特定的令牌。我们传入**所有者**地址，这样我们就可以从我们的**保管账户**中拥有这个钱包，并为**扫描**设置目的地。

# 试验

我们在下面的测试中设定了一些期望。我们需要一个**外部**账户，我们知道其**私有**密钥，所以我们使用*truffle-HD-wallet-privkey*库来模拟一个 exchange 用户向我们生成的钱包地址发送资金。我们还必须创造一个虚拟的 *ERC20* 硬币来测试我们的钱包功能，这个硬币被恰当地命名为*genericer 20*。测试本身应该是不言自明的，但如果你对它的意图有任何疑问，请随时问我。

# 尝试一下

## 环境和回购

您需要安装以下软件

*   加纳切—[https://truffleframework.com/ganache](https://truffleframework.com/ganache)
*   块菌— `npm install -g truffle`

```
# Clone the wallet repository
git clone [git@github.com](mailto:git@github.com):cipherzzz/erc20-megawallet.git# Install the dependencies
npm install
```

## 项目设置

将`.env.example`复制到名为`.env`的新文件中

将 ganache 应用程序中的助记短语添加到您的`.env`

在你的`.env`文件中添加一个除了第一个以外的任何一个 ganache 账户的公钥和私钥(我用的是第六个)。只需点击`key`图标，您将看到公钥和私钥。

您还需要确保您的`truffle.js`中列出的 post 与您的 ganache 服务器运行的端口相匹配。通常不是`7545`就是`8545`。

## 运行它

现在你已经准备好享受你的劳动成果了——看看所有这些是否会奏效。请注意测试的输出，并研究它们以了解隐藏的内容。

```
# Compile the contracts
truffle compile# Migrate/Deploy them to the network(Ganache in this case)
truffle migrate# Run the tests
truffle test
```

# 摘要

这篇文章中有很多高阶概念，一开始可能并不明显。我鼓励大家深入研究这些测试，如果有任何问题，可以问我。这是一个具有挑战性的问题，希望它能给你一些启发，让你在将来使用智能合约来解决自己的问题。这个项目的仓库是[这里](https://github.com/cipherzzz/erc20-megawallet)供你参考。

## 放弃

*请明智一点，不要将本文中的代码投入生产，除非首先进行尽职调查并确保它符合您的安全需求。本文仅供娱乐和教育之用，并不构成对您组织的安全或保管决策的建议。*

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)