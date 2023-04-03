# 以太坊和 EOS 代币的区别

> 原文：<https://medium.com/coinmonks/difference-between-ethereum-and-eos-tokens-f2399051c0b6?source=collection_archive---------6----------------------->

![](img/9b7f3c78aba721b9b9681cf2e3cdea25.png)

## 以太坊有 ERC-20 token，EOS 有 EOSIO.Token，它们的作用是一样的，但是它们一样吗？

[ERC-20 令牌](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md)是以太坊世界事实上的标准。所有钱包、交易所、应用都支持。然而，ERC-20 令牌是一个标准，有一些不同的实现，它们有自己的细微差别。
EOSIO。Token 是 EOS 区块链公司令牌和具体实现的标准。EOS 令牌本身就是 EOSIO 的一个典型例子。象征性合同。

ERC-20 令牌标准定义了以下接口:

```
name – Returns the name of the token - e.g. "MyToken". symbol – Returns the symbol of the token. E.g. "HIX". decimals – Returns the number of decimals the token uses - e.g. 8, means to divide the token amount by 100000000 to get its user representation. totalSupply – Returns the total token supply. balanceOf – Returns the account balance of another account with address _owner. transfer – Transfers _value amount of tokens to address _to, and MUST fire the Transfer event.transferFrom – Transfers _value amount of tokens from address _from to address _to, and MUST fire the Transfer event. The transferFrom method is used for a withdraw workflow, allowing contracts to transfer tokens on your behalf. approve – Allows _spender to withdraw from your account multiple times, up to the _value amount. allowance – Returns the amount which _spender is still allowed to withdraw from _owner. EventsTransfer – MUST trigger when tokens are transferred, including zero value transfers. Approval – MUST trigger on any successful call to approve(address _spender, uint256 _value).
```

如您所见，ERC-20 标准同时处理几件事情:

*   创建您自己的令牌与名称，符号，小数和供应。
*   查询地址和总供应的余额。
*   在账户之间转移代币。
*   允许第三方使用 approve/transferFrom 方法的组合代表用户管理令牌。津贴也可以检查。
*   为每个转移和批准方法调用触发的转移/批准事件。从而提供完整的操作历史。

现在，让我们看看 EOSIO.Token。它定义了以下接口:

```
void create( account_name issuer, asset maximum_supply); 
Creates token with a name, symbol and max supply. void issue( account_name to, asset quantity, string memo);
Issues a certain amount of tokens to an account. void transfer( account_name from, account_name to, asset quantity, string memo); 
Transfers tokens from one account to another. inline asset get_supply( symbol_name sym) const; 
Gets the total supply for a token.inline asset get_balance( account_name owner, symbol_name sym) const; 
Gets token's balance for an account.
```

EOSIO。令牌接口的方法更少，因此可以用于:

*   创建您自己的令牌，包括名称、符号和供应。
*   在帐户的最大供应范围内发行代币。
*   在账户之间转移代币。
*   查询帐户余额和总供应量。

ERC-20 和 EOSIO。令牌共享相同的功能，但 ERC-20 可以做一切 EOSIO。Token 不仅如此。具体来说，它允许第三方代表用户管理令牌，并为每个操作生成转移/批准事件。

**为什么 EOSIO 没有活动。Token？** EOS 区块链[目前没有事件](https://eosio.stackexchange.com/questions/347/is-there-event-emitting-function-in-smart-contract#comment1466_347)。所以这是不可能的。

**为什么 EOSIO 没有第三方功能。Token？**我认为这是一件好事，因为额外的功能是额外攻击媒介的来源，如[“ERC 20 API:Approve/transfer from Methods 上的攻击媒介”](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit#heading=h.wqhvh2y0obwt)。

**EOSIO。令牌是令牌**的最小可行版本，减少了它的攻击面。与此同时，它不会涵盖所有潜在的需求，所以我们肯定会看到新的 EOS 的象征性合同，这将有额外的功能，如暂停，铸造，燃烧，等等。

了解有关 EOSIO.Token 的更多信息。

[](https://hackernoon.com/how-to-create-and-deploy-your-own-eos-token-1f4c9cc0eca1) [## 如何创建和部署您自己的 EOS 令牌

### 我们将弄清楚什么是 EOS 令牌，以及您如何自己创建和部署它。

hackernoon.com](https://hackernoon.com/how-to-create-and-deploy-your-own-eos-token-1f4c9cc0eca1) 

*原载于 2018 年 7 月 24 日* [*ylv.io*](https://ylv.io/differences-between-erc-20/) *。*