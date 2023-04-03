# 如何使用 AccessControl.sol

> 原文：<https://medium.com/coinmonks/how-to-use-accesscontrol-sol-9ea3a57f4b15?source=collection_archive---------0----------------------->

## 新的 OpenZeppelin 访问控制合同的三个配置示例。

![](img/ac882caecbe2172a7d1644175fa79679.png)

Photo by [Scott Webb](https://www.pexels.com/@scottwebb?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) from [Pexels](https://www.pexels.com/photo/bloom-book-botanical-cactus-403571/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

在智能合约中，限制授权用户的访问非常重要。10 亿美元被锁定在 DeFi 申请中，你真的希望每个人都知道他们在做什么。

最近，我与 OpenZeppelin 合作重构了他们广泛使用的 [openzeppelin-contracts](https://github.com/OpenZeppelin/openzeppelin-contracts) 存储库中的访问控制。如果您曾经编写过智能契约，那么您很可能继承了它们的代码。

在本文中，我将向您展示如何使用改进后的 [AccessControl.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol) 来限制对您的智能合约的访问。我将通过三个例子来说明这一点。

但首先，让我告诉你我们是如何来到这里的。如果你对这个合同是如何产生的不感兴趣，只想学习如何使用它，请随意跳过下一部分。

# 我们是如何来到这里的

来到这里对我来说是一个花了一年时间的过程。2019 年 5 月，我第一次有了基于角色的分层控制合同的想法。我[在 Medium](/hackernoon/role-based-access-control-for-the-ethereum-blockchain-bcc9dfbcfe5c) 上发布了代码，以及一些关于 OpenZeppelin 当时在做什么的参考资料。

我为 [AllianceBlock](https://www.allianceblock.io/) 和 [Insig](https://www.techhq.io/) 重用了这些代码，开始确信这个实现提供了一些独特而强大的东西。从这些经验中，我写了第二篇文章，展示何时使用 Ownable.sol、Whitelist.sol 和 RBAC.sol。

为了写那篇文章，我需要深入研究 OpenZeppelin 在访问控制方面做了什么。你不做功课就不会挑战最分叉的可靠性存储库。

OpenZeppelin 团队的人真的很热情，在发表第二篇文章后不久，他们[邀请我参与](https://forum.openzeppelin.com/t/redesigning-access-control-for-the-openzeppelin-contracts/2177)他们访问控制合同的重构。通过合作，我们创建了一个访问控制契约，它比他们以前的方法更灵活，也比我以前的方法更健壮。

[AccessControl.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol) 可以从 [openzeppelin-contracts](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol) 库获得，我不再保留不同的代码库。

有趣的是，作为访问控制有多重要的进一步证明，一个版本的 [Ownable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/694d1afe1e6050d01b54f6c27e915e33a8630c99/contracts/Ownable.sol) 早在 2016 年就存在了。这是 Manuel Araoz 的第九次承诺，也就是后来的 openzeppelin 合同。

在 solidity years，这就像挖掘恐龙的骨头，证明了访问控制是你在编写智能合同时应该首先考虑的事情之一。尤其是如果您打算编写健壮的代码。

故事时间结束了，让我们深入代码。

# AccessControl.sol 如何工作

[AccessControl.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol) 中的角色是一个包含一组地址的结构，代表承担该角色的帐户。所有角色都存储在由字节 32 标识符索引的映射中，对于每个角色都是唯一的。

每个角色还包含另一个角色的 bytes32 标识符，我们称之为其管理角色。

有一些内部函数可以授予和撤销角色，以及将一个角色设置为另一个角色的管理员。这些内部函数没有任何限制，如果您正在扩展 AccessControl.sol，您就可以使用它们，我们将在本文的后面进行扩展。

还有一些外部函数限制授予和撤销角色，以及将角色设置为其他人的管理员。这些函数只能由被授予被修改角色的管理员角色的帐户从外部调用。

例如，假设我们有两个角色，DEFAULT_ADMIN_ROLE 和 USER_ROLE。默认管理员角色是用户角色的管理员角色。

只有被授予 DEFAULT_ADMIN_ROLE 的帐户才能授予或撤销帐户的 USER_ROLE。

同样，只有被授予 DEFAULT_ADMIN_ROLE 的帐户才能重新定义 USER_ROLE 的管理员角色。

在这里， [nventuro](https://github.com/nventuro) 使用了一点魔法，将 DEFAULT_ADMIN_ROLE 定义为未初始化的 bytes32 变量。这意味着默认情况下，所有角色都将 DEFAULT_ADMIN_ROLE 作为管理员角色。

就这样吧，我用例子更详细的给你看。

## 社区

[这个契约](https://github.com/HQ20/contracts/blob/master/contracts/access/Community.sol)复制了之前存在于 OpenZeppelin [Roles.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58a3368215581509d05bd3ec4d53cd381c9bb40e/contracts/access/Roles.sol) 中的功能。

*   只有一个角色(默认管理员角色)
*   任何拥有该角色的人都可以将其授予其他人。
*   在 [Community.sol](https://github.com/HQ20/contracts/blob/master/contracts/access/Community.sol) 中，角色不能从不同的帐户中撤销，但是帐户可以放弃他们拥有的角色。
*   传递给构造函数的地址是第一个被授予角色的帐户。

```
pragma solidity ^0.6.0;
import "@openzeppelin/contracts/access/AccessControl.sol";*///* **@dev** *Implements a single role access control contract.* **contract** Community **is** AccessControl { *///* **@dev** *Create the community role, with `root` as a member.* **constructor** (address root) **public** {
    _setupRole(DEFAULT_ADMIN_ROLE, root);
  } *///* **@dev** *Restricted to members of the community.* **modifier** onlyMember() {
    require(isMember(msg.sender), "Restricted to members.");
    _;
  } *///* **@dev** *Return `true` if the `account` belongs to the community.* **function** isMember(address account)
    **public** **virtual** **view** returns (bool)
  {
    return hasRole(DEFAULT_ADMIN_ROLE, account);
  } *///* **@dev** *Add a member of the community.* **function** addMember(address account) **public** **virtual** onlyMember {
    grantRole(DEFAULT_ADMIN_ROLE, account);
  } *///* **@dev** *Remove oneself as a member of the community.* **function** leaveCommunity() **public** **virtual** {renounceRole(DEFAULT_ADMIN_ROLE, msg.sender);
  }
}
```

## 治理

[这个智能合同](https://github.com/HQ20/contracts/blob/master/contracts/access/Administered.sol)实现了管理员和用户的传统设置。

*   默认管理员角色是用户的管理员角色(默认情况下)。
*   传递给构造函数的地址是初始管理员。
*   管理员可以添加其他管理员。
*   管理员可以授予和撤销任何帐户的用户权限。
*   管理员失去管理员角色的唯一方法是放弃它。

```
**pragma** solidity ^0.6.0;
**import** “@openzeppelin/contracts/access/AccessControl.sol”;/**
 * @title Administered
 * @author Alberto Cuesta Canada
 * @notice Implements Admin and User roles.
 */
**contract** Administered is AccessControl {
  bytes32 public constant USER_ROLE = keccak256(“USER”); /// @dev Add `root` to the admin role as a member.
  **constructor** (address root)
    public
  {
    _setupRole(DEFAULT_ADMIN_ROLE, root);
    _setRoleAdmin(USER_ROLE, DEFAULT_ADMIN_ROLE);
  } /// @dev Restricted to members of the admin role.
  **modifier** onlyAdmin()
  {
    require(isAdmin(msg.sender), “Restricted to admins.”);
    _;
  } /// @dev Restricted to members of the user role.
  **modifier** onlyUser()
  {
    require(isUser(msg.sender), “Restricted to users.”);
    _;
  } /// @dev Return `true` if the account belongs to the admin role.
  **function** isAdmin(address account)
    public virtual view returns (bool)
  {
    return hasRole(DEFAULT_ADMIN_ROLE, account);
  } /// @dev Return `true` if the account belongs to the user role.
  **function** isUser(address account)
    public virtual view returns (bool)
  {
    return hasRole(USER_ROLE, account);
  } /// @dev Add an account to the user role. Restricted to admins.
  **function** addUser(address account)
    public virtual onlyAdmin
  {
    grantRole(USER_ROLE, account);
  } /// @dev Add an account to the admin role. Restricted to admins.
  **function** addAdmin(address account)
    public virtual onlyAdmin
  {
    grantRole(DEFAULT_ADMIN_ROLE, account);
  } /// @dev Remove an account from the user role. Restricted to admins.
  **function** removeUser(address account)
    public virtual onlyAdmin
  {
    revokeRole(USER_ROLE, account);
  } /// @dev Remove oneself from the admin role.
  **function** renounceAdmin()
    public virtual
  {
    renounceRole(DEFAULT_ADMIN_ROLE, msg.sender);
  }
}
```

## 等级制度

这是在 [AccessControl.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol) 中实现的结构，只需做最小的修改就可以安全地构建角色层次。

*   传递给构造函数的地址具有 DEFAULT_ADMIN_ROLE，默认情况下是所有角色的 ADMIN。我将把那个地址称为 root。
*   Root 可以将 DEFAULT_ADMIN_ROLE 授予任何帐户。
*   任何根帐户都可以从任何其他帐户撤销 DEFAULT_ADMIN_ROLE 角色，所以最好不要一开始就授予它，除非您知道自己在做什么。
*   所有角色从一开始就存在，作为映射中的键。DEFAULT_ADMIN_ROLE 中的帐户可以授予或撤销任何角色。
*   在 [AccessControl.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol) 中，每个角色都有一个管理员角色(默认为 DEFAULT_ADMIN_ROLE)。在 [Hierarchy.sol](https://github.com/HQ20/contracts/blob/master/contracts/access/Hierarchy.sol) 中，我们允许任何具有该管理员角色的帐户可以更改关系并选择新的管理员角色。这可以用来建立角色的层次结构，例如:默认管理员角色- >用户管理员- >用户。

```
**contract** Hierarchy **is** AccessControl {
  **event** AdminRoleSet(bytes32 roleId, bytes32 adminRoleId); *///* **@dev** *Add `root` as a member of the root role.* **constructor** (address root) **public** {
    _setupRole(DEFAULT_ADMIN_ROLE, root);
  } *///* **@dev** *Restricted to members of the role passed as a parameter.* **modifier** onlyMember(bytes32 roleId) {
    require(hasRole(roleId, msg.sender), "Restricted to members.");
    _;
  } *///* **@dev** *Create a new role with the specified admin role.* **function** addRole(bytes32 roleId, bytes32 adminRoleId)
    **public** onlyMember(adminRoleId)
  {
    _setRoleAdmin(roleId, adminRoleId);
  }
}
```

手头有 AccessControl.sol 相关代码对于理解正在发生的事情很有用。

```
**abstract** **contract** AccessControl **is** Context {
… *///* **@dev** *Grants `role` to `account`.* **function** grantRole(bytes32 role, address account)
    **external** **virtual** {
    require(
      hasRole(_roles[role].adminRole, _msgSender()),
      “AccessControl: sender must be an admin to grant”);
    _grantRole(role, account);
  } *///* **@dev** *Revokes `role` from `account`.* **function** revokeRole(bytes32 role, address account)
    **external** **virtual** {
    require(
      hasRole(_roles[role].adminRole, _msgSender()),
      “AccessControl: sender must be an admin to revoke”);
    _revokeRole(role, account);
  } *///* **@dev** *Revokes `role` from the calling account.* **function** renounceRole(bytes32 role, address account)
    **external** **virtual** {
    require(
      account == _msgSender(),
      “AccessControl: can only renounce roles for self”);
    _revokeRole(role, account);
  }
  …
}
```

# 结论

我真的很开心，也很自豪能走到这一步。

真的很自豪，因为为 [openzeppelin-contracts](https://github.com/OpenZeppelin/openzeppelin-contracts) 贡献代码和想法是对一个聪明的合同开发者最强有力的验证。

真的很高兴，因为我一直使用的访问控制合同现在终于通过了彻底的审核，并由他们领域的大师们进行了完善。我现在可以完全自信地使用它们，相信它们是正确的。

为社区做贡献感到自豪和高兴。回馈是最好的礼物。

现在由你来继续。像往常一样，如果你有一个有趣的项目，需要一些关于使用这些功能的建议，[请联系](http://www.albertocuesta.es)。BUIDL！

[![](img/e9dbce386c4f90837b5db529a4c87766.png)](https://coincodecap.com)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)