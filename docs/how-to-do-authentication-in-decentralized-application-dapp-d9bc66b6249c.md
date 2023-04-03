# 如何在分散式应用程序(DAPP)中进行认证

> 原文：<https://medium.com/coinmonks/how-to-do-authentication-in-decentralized-application-dapp-d9bc66b6249c?source=collection_archive---------1----------------------->

## 在这篇博客中，我将告诉你我们如何给 DApps 添加认证

# 介绍

区块链技术与日俱增，它对不同行业产生了巨大影响，区块链也存在一些挑战，如果我们谈论 DApps(分散式应用程序), DApps 的主要挑战是对用户进行身份验证，在集中式应用程序中，我们有不同的身份验证提供商，如 firebase 身份验证和社交媒体登录集成(脸书、谷歌、Github..但是在 DApps 中有两种方法来认证用户，我们将详细讨论第二种方法**使用智能合约代码**。

**DApp 认证用户的两种方式**

1.  元掩码一键登录
2.  使用智能合同代码

# 元掩码一键登录:

使用 MetaMask 扩展的一键式密码安全登录流程，所有数据都存储在我们自己的后端。我们称之为:“用元掩码登录”。

文档链接:[https://www . toptal . com/ether eum/one-click-log in-flows-a-meta mask-tutorial](https://www.toptal.com/ethereum/one-click-login-flows-a-metamask-tutorial)

# 使用智能合同代码

为了在 Dapp 中认证用户，我用 Solidity 语言写了一个智能合约 Auth.sol。它使用 Solidity 语言和编写的(Auth.sol)智能契约在以太坊区块链上提供去中心化应用中的认证，以实现 Dapps 中的认证功能。

Github 资源库链接:[https://github.com/Zaryab-Programmer/dapp-authentication](https://github.com/Zaryab-Programmer/dapp-authentication)

**智能合约代码:**

在这里，契约名是 **Auth** ，它内部有 **struct UserDetail** 。

```
struct UserDetail {address addr;string name;string password;string CNIC;bool isUserLoggedIn;}
```

映射**地址= >用户详细信息**

```
mapping(address => UserDetail) user;
```

在用户注册功能中，用户可以在元掩码上进行交易，通过**要求**检查功能检查用户是否已经注册。如果用户尚未注册，则在区块链上注册用户数据。

```
function register(address _address,string memory _name,string memory _password,string memory _cnic) public returns (bool) {require(user[_address].addr != *msg.sender*);user[_address].addr = _address;user[_address].name = _name;user[_address].password = _password;user[_address].CNIC = _cnic;user[_address].isUserLoggedIn = false;return true;}
```

登录功能提供了在 Dapp 上登录的功能，将字段 **isUserLoggedIn** 更改为 **true，**表示用户登录成功。**checkisuserloged**函数检查用户是否登录，它返回一个布尔值，该值有助于验证用户。

```
function login(address _address, string memory _password) public returns (bool) {if ( keccak256(*abi*.encodePacked(user[_address].password)) == keccak256(*abi*.encodePacked(_password)) ) { user[_address].isUserLoggedIn = true; return user[_address].isUserLoggedIn;} else { return false; }
}function checkIsUserLogged(address _address) public view returns (bool) {return (user[_address].isUserLoggedIn);}
```

注销功能提供了通过执行交易和更改**用户[_ 地址]的字段来注销用户的功能。isUserLoggedIn = false**

```
function logout(address _address) public {user[_address].isUserLoggedIn = false;}
```

**由投稿和帮助本博客撰写:**

*   艾米尔·阿里·里兹维先生(区块链专家)
*   COMSATS Sahiwal 讲师
*   纳西尔·迈赫迪先生
*   COMSATS Sahiwal 讲师

## 另外，阅读

> 加入 [Coinmonks 电报频道](https://t.me/coincodecap)，了解加密交易和投资

[](https://blog.coincodecap.com/smart-contract-smartpy) [## 如何使用 SmartPy 编写智能合同？

### 在本文中，您将学习使用 SmartPy 编写智能合同。智能合同是存储在…

blog.coincodecap.com](https://blog.coincodecap.com/smart-contract-smartpy)