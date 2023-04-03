# 使用 hard hat & Truffle react 项目设置—第 2 部分

> 原文：<https://medium.com/coinmonks/react-project-setup-using-hardhat-truffle-part-2-2872f20bf7ca?source=collection_archive---------4----------------------->

![](img/c94bbe96b8b36731c12454f4640c14ca.png)

Photo by [Kevin Canlas](https://unsplash.com/@kvncnls?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

现在我们已经从第 1 部分开始设置了项目，在开始写合同之前，我们必须做一些配置。

## 配置设置

每个项目现在应该在根目录中有一个配置文件，它应该被命名为 Truffle。

```
truffle-config.js
```

对于安全帽，它应该被命名为

```
Hardhat-config.js
```

让我们配置我们的编译器版本的可靠性，以及我们将部署我们的合同的网络

# 网络

这是指我们计划在哪里部署我们的合同并与之交互。

> 在本例中，我们将使用本地区块链进行部署，因为我们将获得帐户、私钥，并且已经预先获得了测试醚的资金

**本地** —在我们的电脑上运行一个版本的区块链

> 当使用 testnet 选项时，必须创建一个钱包，并且您必须从所选 testnet 的水龙头获得假乙醚进行测试。 [**Metamask**](https://metamask.io) 是钱包的一个选项，相当容易使用

Testnet——一个以太坊主网的模拟，在那里你使用假的以太网来支付合同交互和汽油费，下面是 testnest 的列表:

*   [Ropsten](https://faucet.ropsten.be)
*   [科万](https://github.com/kovan-testnet)
*   [林克比](https://faucet.rinkeby.io)
*   戈利

> 真实的东西，并使用真实的以太进行互动

**Mainnet** —以太坊区块链

# 块菌配置

首先，我们将把 Solidity 编译器设置为我们将在智能合约中使用的版本。

打开 **truffle-config.js** 文件，在编译器对象下，转到 **solc:** 并将其设置为版本 **0.8.6** 。

> 您可以选择将其他参数注释掉，或者删除它们，因为本例不会用到它们。它被注释掉，以便与您可能看到的示例尽可能接近。

下一步是建立我们的网络。

Truffle 提供了两种方法在我们的计算机上使用 [**Ganache**](https://www.trufflesuite.com/ganache) 创建本地区块链。

*   加纳切 CLI
*   [加纳切 GUI](https://www.trufflesuite.com/ganache)

我们将走使用 [**Ganache CLI**](https://github.com/trufflesuite/ganache-cli-archive) 的路线，但是也可以随意试用**[**Ganache GUI**](https://www.trufflesuite.com/ganache)。**

**在项目中本地安装 [**Ganache CLI**](https://github.com/trufflesuite/ganache-cli-archive) 。**

```
npm i ganache-cli
```

**或在您的计算机上全局显示**

```
npm i -g ganache-cli
```

**一旦安装完成，我们将通过取消对开发对象、主机、端口和 network_id 的注释，在 truffle-config.js 中设置它。**

> **更多关于松露配置的信息可以在 [**这里**](https://www.trufflesuite.com/docs/truffle/reference/configuration/) 找到。**

**[https://gist . github . com/Shih-Yu/440d 791557 EB 0 E1 ce 8 c 47 Baba 9 Fe 0680](https://gist.github.com/Shih-Yu/440d791557eb0e1ce8c47baba9fe0680)**

**既然已经配置了 Ganache，那么让我们到我们终端来运行它。**

> **这应该在与智能合约交互时运行。**

```
ganache-cli
```

**现在，我们的计算机上有了一个区块链，它有 10 个账户，预先注入了 100 个醚和它们的私钥，以及助记符种子短语。**

# ****安全帽配置****

**首先，我们将把 Solidity 编译器设置为我们将在智能合约中使用的版本。**

**打开 **hardhat.config.js** 文件，在 **module.exports** 下，转到 **solidity:** 并将其设置为版本 **0.8.6** 。**

> **Hardhat 已经将其设置为 **0.8.4****

**下一步是建立我们的网络。**

**由于没有提供格式，我们将这样设置它:**

> **更多关于安全帽配置的信息可以在 [**这里**](https://hardhat.org/config/) 找到。**

**现在 Hardhat 已经配置好了，让我们到我们的终端来运行它。**

```
npx hardhat node
```

**现在我们的计算机上有一个区块链，它有 20 个账户，预先注入了 1000 个乙醚和它们的私钥**

## **在第 3 部分中，我们将编写一个简单的智能契约。[点击此处查看第三部分](/@shihyuhwang/project-setup-using-hardhat-truffle-part-3-724ab0d2a1fe)。**

> **加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/channel/UCbyDhTbOiKh2iUMKBi4-4Zg)了解加密交易和投资**

## **也阅读**

**[](https://blog.coincodecap.com/crypto-exchange) [## 最佳加密交易所| 2021 年十大加密货币交易所

### 加密货币交易所的加密交易需要了解市场，这可以帮助你获得利润…

blog.coincodecap.com](https://blog.coincodecap.com/crypto-exchange) [](https://blog.coincodecap.com/crypto-lending) [## 2021 年 9 大最佳加密贷款平台| CoinCodeCap

### 当谈到加密货币贷款时，大量因素等同于良好的收入状况。此外，借款的一部分…

blog.coincodecap.com](https://blog.coincodecap.com/crypto-lending) [](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [## 2021 年最佳加密交易机器人(免费和付费)

### 2021 年币安、比特币基地、库币和其他密码交易所的最佳密码交易机器人。四进制，位间隙…

medium.com](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [](https://blog.coincodecap.com/best-hardware-wallet-bitcoin) [## 存储比特币的最佳加密硬件钱包[2021]

### 保管您的数字资产很容易，但找到正确的存储方式却是一项繁琐的任务。在线钱包有一个风险…

blog.coincodecap.com](https://blog.coincodecap.com/best-hardware-wallet-bitcoin)**