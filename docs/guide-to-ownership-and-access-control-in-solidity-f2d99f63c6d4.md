# Solidity 中的所有权和访问控制指南

> 原文：<https://medium.com/coinmonks/guide-to-ownership-and-access-control-in-solidity-f2d99f63c6d4?source=collection_archive---------0----------------------->

## 从简单到复杂，代码可以重用。

![](img/2c344078a13a9d720a330840c5ad930e.png)

【2020 年 5 月更新:智能合约开发进展非常快，本文描述的情况已经发生了变化。我与 OpenZeppelin 合作开发了他们 3.0 版本的访问控制库，在这里描述的智能契约中，只有 [Ownable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v2.5.0/contracts/ownership/Ownable.sol) 还在使用。请阅读[的另一篇文章](/coinmonks/how-to-use-accesscontrol-sol-9ea3a57f4b15)，了解在智能合同中使用访问控制的最新指南。

# 介绍

当撰写智能合同时，我倾向于采取教育的方法。即使它们是为生产环境设计的，我也尽可能让它们易于理解。我编写的[契约](https://github.com/HQ20/contracts)是可重用的，但通常它们会针对每个特定的业务案例进行重写。

在这篇文章中，我将讨论 solidity 智能合同中的三种许可方法。这些方法按照复杂程度的递增顺序进行讨论，这也是您在项目中应该考虑的顺序。我包含了您可以为每种方法重用的代码。

本文假设您习惯于在 solidity 中编写智能契约，并使用继承和将契约地址作为参数传递等特性。如果你正在寻找一篇关于智能合同开发的更简单的文章，你可以试试这篇文章。

> 不熟悉加密？尝试[加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)或[复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)

# 简单方法— Ownable.sol

OpenZeppelin 的 Ownable.sol 合同肯定是重用率最高的合同之一。在 77 行中，它实现了:

1.  断言某人是合同所有者的逻辑。
2.  将函数调用限制为合同所有者以继承合同的逻辑。
3.  将所有权转移到不同地址的逻辑。

在编写智能合约的代码时，经常会用到“继承”。让我们用一个例子来看看如何使用`Ownable`。想象一下，你想在合同中保留一个地址列表，但你想成为唯一一个可以添加更多地址的人。你可以把它想象成一个你信任的人的注册表。你可以这样做:

```
contract Whitelist is Ownable {
   mapping (address => bool) members;

   constructor() public Ownable() {
   }

   function addMember(address _member)
       public
       onlyOwner
   {
       members[_member] = true;
   }
}
```

继承自`Ownable`并在您的上呼叫其建构函式，可确保部署合约的位址已注册为拥有者。`onlyOwner`修饰词如果不被注册为所有者的地址调用，则使功能回复。

部署此合约后，只有您或您指定的某个人可以向中的列表添加新成员。

简单地说，就是这样。还有几个功能，不过如果你查看一下[源代码](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v2.5.0/contracts/ownership/Ownable.sol)，你就会明白了。试着用[实现一些简单的东西](https://github.com/HQ20/contracts/blob/v0.0.2/contracts/access/Whitelist.sol)，这很容易理解。

尽管它有用，也会有很多时候`Ownable`是不够的。在给定的时间内，只有一个地址可以是所有者，只有所有者才能决定谁可以是新所有者，您只能检查您是否是所有者，而不是别人是否是。

# 中间方法—白名单. sol

[白名单。sol](https://github.com/HQ20/contracts/blob/v0.0.2/contracts/access/Whitelist.sol) 保存一个地址列表，该列表可用于限制功能或任何其他目的。它在功能上与 OpenZeppelin 的 [Roles.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v2.5.0/contracts/access/Roles.sol) 非常相似，只是有一些关键的区别(请查看我们的 [README](https://github.com/HQ20/contracts/tree/dev/contracts/access#whitelistsol-vs-openzeppelins-rolessol-and-whitelistedrolesol) )。

[白名单. sol](https://github.com/HQ20/contracts/blob/v0.0.2/contracts/access/Whitelist.sol) 只有三个功能:

```
function isMember(address _member) public view returns(bool);
function addMember(address _member) public onlyOwner;
function removeMember(address _member) public onlyOwner;
```

例如，使用这个合约，您可以保留一个已批准的涉众列表，这些涉众可以是令牌转移的唯一接受者。你可以这样做:

```
pragma solidity ^0.5.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "../access/Whitelist.sol";

contract ERC20Whitelisted is ERC20 {

   Whitelist whitelist;

   constructor(address _whitelistAddress) public {
       whitelist = Whitelist(_whitelistAddress);
   }

   function transfer(address account, uint256 amount) public {
       require(whitelist.isMember(account), "Account not whitelisted.");
       super._transfer(account, amount);
   }
}
```

在上面的例子中，你也可以让`ERC20Whitelisted`继承`ERC20`和`Whitelist`。我很乐意讨论一些权衡。

简单的白名单可能非常强大。OpenZeppelin 使用它们实现了许多 [ERC20](https://github.com/OpenZeppelin/openzeppelin-contracts/tree/release-v2.5.0/contracts/token/ERC20) 和 [ERC721](https://github.com/OpenZeppelin/openzeppelin-contracts/tree/release-v2.5.0/contracts/token/ERC721) 变种，并设法提供了比我们大多数人所需更多的功能。在 [TechHQ](www.techhq.io) 我们也只使用白名单实现了 [CementDAO](https://www.cementdao.com/) 。

然而，有时白名单也会有所欠缺。一个白名单可能需要多个所有者。或者您可能需要管理许多重叠的白名单。对于这些情况，我们有一个等级角色契约。

# 复杂 RBAC.sol

我们开发了 [RBAC.sol](https://github.com/HQ20/contracts/blob/v0.0.2/contracts/access/RBAC.sol) 旨在提供多用户功能，就像现代共享系统一样。

1.  有些角色只不过是一组地址。
2.  组成员资格只能由某些管理员角色的成员修改。
3.  新角色可以在运行时创建。
4.  可以验证角色成员资格。

在低层次上，我们使用用户选择的`bytes32`参数来识别角色。通常这些是可识别的短字符串，但是您也可以使用加密值或地址。

角色本身是一组成员地址和管理角色的标识符。有趣的是，我们不需要将角色的标识符存储在它自己的结构中。

```
struct Role {
    bytes32 adminRoleId;
    mapping (address => bool) members;
}
```

现在有两种方法可以添加新角色并验证角色是否存在:

```
function roleExists(bytes32 _roleId) public view returns(bool);
function addRole(bytes32 _roleId, bytes32 _adminRoleId) public;
```

管理成员的功能是相同的，只是现在必须指定相关的角色:

```
function isMember(address _member, bytes32 _roleId) public view returns(bool);
function addMember(address _member, bytes32 _roleId) public;
function removeMember(address _member, bytes32 _roleId) public;
```

只有当调用者属于我们正在添加成员的角色的管理员角色时，`addMember`和`removeMember`才会成功。

`addRole`只有当调用方属于将管理正在创建的角色的角色时，才会成功。

这些简单的规则将允许创建一个角色层次结构，然后可以用它来实现具有不同许可级别或区域的复杂多用户平台。

# 进一步学习

为了更深入地探究兔子洞，我建议从《T4》杂志的本期[开始。他们的代码库和我们的没什么不同，你会发现大多数设计决策都有一个完整的推理，即使我们选择了另一条路。他们对像`ERC20Mintable`这样的合同使用`Roles`是替代`Whitelist`的一个很好的例子。](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/1772)

勇敢者的另一个资源是[阿拉贡诺斯 ACL 合同](https://github.com/aragon/aragonOS/tree/next/contracts/acl)。只需浏览一下界面，就能看出他们已经决定比其他人走得更远:

```
function hasPermission(address who, address where, bytes32 what, bytes how) public view returns (bool);
```

对于我们自己的 [@hq20/contracts](https://github.com/HQ20/contracts) 包中的例子，我们使用了本文中描述的三个访问控制级别，因此您也应该密切关注。

# 结论

当谈到智能契约实现时，最好只实现所需的复杂性，不要再多了。就许可而言，有三个不同层次的复杂性:

1.  单一用户
2.  用户组
3.  用户组的层次结构

您可以将 [Ownable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v2.5.0/contracts/ownership/Ownable.sol) 用于允许单个用户使用的系统。对于需要在一个组中授权用户的系统，可以使用[@ open zeppelin/roles . sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v2.5.0/contracts/access/Roles.sol)或者 [@hq20/Whitelist.sol](https://github.com/HQ20/contracts/blob/v0.0.2/contracts/access/Whitelist.sol) 。对于需要组层次结构的系统，我们过去已经成功地使用了 [@hq20/RBAC.sol](https://github.com/HQ20/contracts/blob/v0.0.2/contracts/access/RBAC.sol) 。

你会有自己的要求，需要自己做出权衡取舍的决定。了解每个实现背后的设计决策将允许您使用现有的契约或者修改一个供自己使用的契约。

请确保[让我们知道](https://www.techhq.io/#GetinTouch)任何反馈。我们正在开发 [@hq20/contracts](https://github.com/HQ20/contracts) 包来支持真实世界区块链应用程序的编码。我们的目标是让我们的代码被重用和滥用，我们很乐意知道你们是如何做到的。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [杠杆代币的终极指南](/coinmonks/leveraged-token-3f5257808b22)