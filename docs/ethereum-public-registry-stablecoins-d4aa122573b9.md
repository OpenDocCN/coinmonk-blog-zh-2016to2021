# 以太坊公共注册表:Stablecoins

> 原文：<https://medium.com/coinmonks/ethereum-public-registry-stablecoins-d4aa122573b9?source=collection_archive---------5----------------------->

## 实施以太坊智能合约，以实现对 ERC20-Stablecoins 的简单和简化的访问(面向开发人员)。

约翰·夸恩斯特罗姆

![](img/7182ceb59f40c6bea0df223078e2a361.png)

Accessing decentralized information in a centralized registry.

什么是公共注册管理机构？

公众可随时获取的信息的集中收集。在以太坊的情况下，开发人员将受益于所有可用 ERC20-Stablecoins 的广泛列表，这本身就是一个智能合约，允许通过另一个智能合约直接访问信息。

**为什么要设立以太坊注册中心？**

增强的公共注册表提供了对任何所需智能合约的功能的直接访问，这使得开发和设计您自己的智能合约更容易，因为其本质是将功能压缩到一行中。

开发人员通常需要他们正在使用的任何 Stablecoin 的三个关键信息/功能(假设是 ERC20 或 ERC223)。

1.  **稳定币智能合约的地址。** *这是存储各方余额并允许转账或折让的地址。*
2.  **稳定币地址的余额。** *这将在支付处理的情况下启用某些逻辑实现。*
3.  **传送机制。** *这允许某人将他们的稳定积分发送给另一方。*

以下是 Stablecoin 注册表的一个示例:

```
**// State variable.**
mapping(bytes32 => address) public StablecoinRegistry; **// Constructor.**
constructor StablecoinRegistry() public { ... } **// Populate the registry.**
function addStablecoin(bytes32 _name, address _stablecoinAddress) { **// Prevent overwrite.**
    require(StablecoinRegistry[_name] == 0x0); **// Add address to mapping.**
    StablecoinRegistry[_name] = _stablecoinAddress;}
```

一旦这个契约被部署到网络上，并由所有者用 addStablecoin()方法填充，任何人都可以作为接口访问它。

下面是一个使用智能合同注册表的简短示例。
*为简单起见，我们假设之前的合同部署在 0x123。*

```
**// Interface for ERC20 contract.**
contract ERC20 {
  **function** balanceOf(address tokenOwner) returns (uint balance);
  **function** transfer(address to, uint tokens) returns (bool success);
} **// Example contract (some code omitted).**
contract Example { **// State variable.**
  address public Registry = 0x123; **// Constructor.**
  constructor PaymentManagement() public { ... } **// Feature 1 - Balance** function returnBalance(address _user, bytes32 _contract) {
      ERC20 = ERC20( Registry.StabelcoinRegistry[_contract] );
      return ERC20.balanceOf(_user);
  } **// Feature 2 - Transfer** function transferTo(address _user, bytes32 _contract, uint _amt) {
      ERC20 = ERC20( Registry.StabelcoinRegistry[_contract] );
      return ERC20.transfer(_user, _amt);
  }}
```

同样，这里的关键是构建一个网络中所有开发人员都可以访问的库。可以说，如果值得信赖的一方能够维护其数据的真实性和相关性，那么为所有 ERC20 令牌而不仅仅是 Stablecoins 建立一个公共注册表将证明是有价值的。

请记住，上面的转移功能将仅转移智能合约本身中持有的令牌(出于托管目的)。除非您发布的智能合约在主 Stablecoin 智能合约中明确包含津贴，否则它将无法为其他用户转移代币。

> **约翰夸恩斯特罗姆**
> www.inveth.io(暂时离线)