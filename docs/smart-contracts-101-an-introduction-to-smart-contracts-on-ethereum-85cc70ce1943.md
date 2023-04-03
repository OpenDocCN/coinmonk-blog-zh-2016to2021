# 智能合约 101:以太坊智能合约简介

> 原文：<https://medium.com/coinmonks/smart-contracts-101-an-introduction-to-smart-contracts-on-ethereum-85cc70ce1943?source=collection_archive---------1----------------------->

## 以太坊智能合约

![](img/55d382cca48cffe5fda3fe27f2d8d85e.png)

Photo by [Hans Reniers](https://unsplash.com/@hansreniers?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 在开始之前

本系列文章旨在向具有 IT 和编程基础知识的新手介绍智能合约，并涵盖与智能合约开发相关的各个方面。我的目标是，在本系列结束时，您将对最重要的主题和底层技术有一个广泛的了解，并且您将能够开发和部署您自己的安全和高效的合同到以太坊区块链。

# 什么是智能合同？

关于什么是智能合同，有很多误解。混淆可能源于术语*合同*包含在给予本发明的名称中，这导致人们认为它在某种程度上与法律合同相关。然而事实并非如此。

简而言之，智能合同是一个位于区块链上的*分散程序*。

术语*程序*是不言自明的——它是一组可以执行的指令。但是*去中心化*在这个上下文中是什么意思呢？这意味着适用于分散化区块链支付的所有好处——开放区块链的五大支柱——也适用于该计划:[开放、公共、无国界、中立和抵制审查](https://www.youtube.com/watch?v=qlAhXo-d-64)。

**但是等等，并不是所有的合同都符合这些标准！** 在撰写本文时，区块链以太坊上使用最多的智能合约之一是 *USDT (Tether)* 代币合约，管理着超过 100 亿美元。它的令牌应该代表以太坊上的美元。下面是一段有趣的源代码摘录:

```
function addBlackList (address _evilUser) public onlyOwner {
    isBlackListed[_evilUser] = true;
    AddedBlackList(_evilUser);
}function destroyBlackFunds (address _blackListedUser) public onlyOwner {
    require(isBlackListed[_blackListedUser]);
    uint dirtyFunds = balanceOf(_blackListedUser);
    balances[_blackListedUser] = 0;
    _totalSupply -= dirtyFunds;
    DestroyedBlackFunds(_blackListedUser, dirtyFunds);
}
```

如果您现在还不理解语法，请不要担心，我们稍后会介绍这一点！

该合同支持两个功能: *addBlackList* 将一个地址添加到黑名单中，以及 *destroyBlackFunds* 销毁黑名单地址中的所有资金。 *onlyOwner* 表示这些功能只能由管理员执行。

在这种情况下，管理员指的是[栓系金库](https://tether.to/)。USDT 代币背后的公司处理发行和 1:1 支持法定货币的 USDT 代币。

**那么现在的五大支柱呢？这是我们得出一个非常重要的区别的地方:智能合约的一般概念和特定智能合约的实现是两回事。**

每个人都可以得到 USDT 合同。无论你来自哪个国家、年龄、种族或信用评分，你都可以与之互动。你可以查看它的源代码，地址和验证它执行的每一个动作。它的历史铭刻在以太坊区块链，无法删除。

然而，通过与智能契约交互，你必须遵守它用代码设定的规则。毫无疑问，在分散的系统之上建立一个高度集中的系统是可能的。事实上，USDT 是使用最多的合同之一，这表明这种想法还没有达到大多数用户。

# 摘要

本系列的第一篇文章旨在让您对智能合约背后的概念有一个抽象的理解。在[系列的下一篇文章](/@bobface/smart-contracts-101-the-ethereum-virtual-machine-cc58c12468bf)中，我们将通过探索以太坊虚拟机(EVM)开始深入技术内容。

*我很高兴收到您对以下帖子内容的反馈和建议。随时联系我！*

## **还有，读**

*   [顶级 DeFi 项目](/coinmonks/defi-future-10-promising-projects-in-the-defi-world-ff2b697ab006)
*   [unis WAP 最佳钱包](/coinmonks/best-wallets-to-use-uniswap-e91a6385d9e8)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)