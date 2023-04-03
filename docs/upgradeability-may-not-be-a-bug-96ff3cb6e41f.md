# 可升级性可能(不)是一个缺陷

> 原文：<https://medium.com/coinmonks/upgradeability-may-not-be-a-bug-96ff3cb6e41f?source=collection_archive---------2----------------------->

受[可升级性的启发，是一个 Bug](/consensys-diligence/upgradeability-is-a-bug-dba0203152ce) 和一个**真实的 ERC-20 令牌故事。**

我基本同意史蒂文斯的文章。
但是，如果你公司的主要产品之一应该是运行在以太坊区块链上的不可阻挡的代码，并与智能合约进行交互，该怎么办呢？

*   不符合标准的接口；
*   不可预测的业务逻辑/接口实现？

将所有东西混合在一起:

*   整个以太坊环境的(不那么)早期阶段；
*   初创企业的热潮瞄准了一个“当蓝宝”的 ICO
*   相信只要一点点“复制和粘贴”就足以创建一个 ERC-20 令牌智能合约

您将最终设计出一个可升级的智能合约。

这是我们面临的挑战之一。
我们的混合分散式交易所解决方案的一部分是为用户提供一个智能合约，又名 *TradingWallet* ，用于 ERC-20 令牌管理。

从架构的角度来看，我们将 *TradingWallet* 设计为三个智能合约的组合:

*   ***钱包*** 智能契约:跟踪状态变量，定义 *TradingWallet* 功能接口；
*   ***walletlogicv 1/2/3…****智能合约 *:* 实现上述接口的业务逻辑；*
*   ****Wallet connector***智能合约:连接*钱包* SC 和 *WalletLogicVx* SC。*

*基本上*钱包*智能契约是使用*将一个函数调用重定向到*WalletLogicVx**底层正确的函数实现。delegatecall()。***

> **因此，如果 TradingWallet 业务逻辑可以在幕后改变，那么可升级的智能合约怎么可能是不可变的和可信的呢？**
> 
> **放弃权力以支持其用户，即合同所有者。**

**每次自动部署新的*交易平台*时，它将连接到特定用户，更具体地说，连接到用户的*外部拥有的账户*。
因此合同业主可以:**

*   **同意使用当前公共 WalletLogicVx 创建他/她的 *TradingWallet。***
*   **管理他/她的资产；**
*   **然后可能同意他/她的 *TradingWallet* 的 *current WalletLogicVx* 的更新，可能会修复一些 bug。**

**可以想象， *TradingWallet* 的目的是管理 ETH 和令牌进行交易。
ERC-20 令牌是由通用接口定义的智能合约:**

**[https://github.com/OpenZeppelin/openzeppelin-solidity](https://github.com/OpenZeppelin/openzeppelin-solidity)**

**不幸的是，有许多令牌不满足上面的接口。
事实上，对于像*转移*或*转移自*函数*这样的关键函数来说，有很多令牌**不返回任何值**。***

**很难理解用户*trading wallet***deposit**函数调用是否真正成功。
在更改任何其他 EVM 状态之前，确保转移成功的最常用方法是:**

***要求(IERC20(_tokenAddress)。transferFrom(msg.sender，this，_amount)，…)；***

**但是由于最近添加的[*RETURNDATASIZE*](https://www.ethervm.io/#3D)*操作码和返回的数据大小比预期的短的坏令牌，上述 ***要求*** 回复 EVM 状态转换，即使传输实际上会成功。***

**![](img/20e315098e4437b75e880b5b584b1f99.png)**

> **那么，我们如何在不中断服务的情况下处理这个问题呢？**
> 
> **编写一些汇编，向我们的用户解释和建议 TradingWallet 的升级。**

**如果你喜欢这篇文章，点击下面的 ***推荐*** *按钮会很棒，这意味着会有更多的人看到它。你也可以在* [*推特*](https://twitter.com/andreafspeziale) *上找到我。谢谢！***

> **[直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)**

**[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)**