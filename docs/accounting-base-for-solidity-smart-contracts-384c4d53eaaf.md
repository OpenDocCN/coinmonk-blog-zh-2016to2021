# 可靠性智能合同的会计基础

> 原文：<https://medium.com/coinmonks/accounting-base-for-solidity-smart-contracts-384c4d53eaaf?source=collection_archive---------6----------------------->

![](img/97cac8538fbf0b3c3c2619029692b19c.png)

以太坊智能合约正被用来处理数十亿美元的价值，但令人惊讶的是，我还没有遇到一个处理*会计*的通用模式或库。在智能合约的上下文中，记账意味着以一种安全但易于理解的方式处理 ether 和 ERC20 令牌。

**为什么这是必要的？**在开发智能合同时，有许多安全注意事项和细微差别需要考虑，但是如果有一个普遍接受和审核的模式，那么您不必在每次需要时都自己实现它。发送、接收和处理以太网和令牌可能是大多数智能合约最常见的功能之一，所以我提出了一个基本的 [Accounting.sol](https://github.com/paradoxDAO/accounting/blob/master/contracts/Accounting.sol) 可以用作任何想要轻松处理不同帐户的智能合约的父合约。我将在下面概述会计的特性和使用案例:

# 详细描述

首先，我使用 [DS-Math](https://github.com/dapphub/ds-math/blob/master/src/math.sol) 进行安全的算术运算，但是为了简单起见，我把它变成了一个库，因为它原本是一个契约(我简单地把`library`的关键字改成了`contract`)。

```
uint public totalETH; 
mapping (address => uint) public totalTokenBalances;
```

第二，契约跟踪所计算的总 ETH 和令牌，因为 ETH 和令牌余额都可以在不执行任何代码的情况下改变。帐户是一种结构:

```
struct Account {
	bytes32 name; 
	uint balanceETH;
	mapping (address => uint) tokenBalances;
}
```

该名称可能不是每次使用都必须的，但为了清楚起见，还是包括在内。它不是强制设置的。会计基础有一个可用作默认帐户的“基础”帐户:

```
Account base = Account({
	name: "Base",
	balanceETH: 0
});
```

不同的可能事务有不同的事件:

```
event **ETHDeposited**(bytes32 indexed account, address indexed from, uint value);     
event **ETHSent**(bytes32 indexed account, address indexed to, uint value);     
event **ETHTransferred**(bytes32 indexed fromAccount, bytes32 indexed toAccount, uint value);     
event **TokenTransferred**(bytes32 indexed fromAccount, bytes32 indexed toAccount, address indexed token, uint value);     
event **TokenDeposited**(bytes32 indexed account, address indexed token, address indexed from, uint value);         
event **TokenSent**(bytes32 indexed account, address indexed token, address indexed to, uint value);
```

如您所见，您可以存放 ETH 和令牌，可以在内部帐户之间转移它们，还可以发送它们。

存放乙醚和代币:

```
function **depositETH**(Account storage a, address _from, uint _value) internal {
         a.balanceETH = a.balanceETH.add(_value);
         totalETH = totalETH.add(_value);
         emit ETHDeposited(a.name, _from, _value);     
}function **depositToken**(Account storage a, address _token, address _from, uint _value)      
internal noReentrance {
	require(ERC20(_token).transferFrom(_from, address(this), 	_value));
        totalTokenBalances[_token] = totalTokenBalances[_token].add	(_value);
        a.tokenBalances[_token] = a.tokenBalances[_token].add	(_value);
        emit TokenDeposited(a.name, _token, _from, _value);     
}
```

这些函数是内部的，所以您只需要在您的应付款和代币存款函数中使用它们。您自己为 ether 指定帐户和值(ether deposit 函数实际上不会检查消息的值)。存放令牌需要批准，因此该函数将实际调用令牌契约并转移它们。

您可以发送以太网和令牌，也可以与以太网进行交易，而无需担心重入、更新和检查余额:

```
function **sendETH**(Account storage a, address _to, uint _value)      internal noReentrance {
         require(a.balanceETH >= _value);
         require(_to != address(0));
         a.balanceETH = a.balanceETH.sub(_value);
         totalETH = totalETH.sub(_value);
         _to.transfer(_value);
         emit ETHSent(a.name, _to, _value);
}function **transact**(Account storage a, address _to, uint _value, bytes data) internal noReentrance
{
         require(a.balanceETH >= _value);
         require(_to != address(0));
         a.balanceETH = a.balanceETH.sub(_value);
         totalETH = totalETH.sub(_value);
         require(_to.call.value(_value)(data));                  	 emit ETHSent(a.name, _to, _value);
}function **sendToken**(Account storage a, address _token, address _to, uint _value)      internal noReentrance      
{
         require(a.tokenBalances[_token] >= _value);
         require(_to != address(0));
         a.tokenBalances[_token] = a.tokenBalances[_token].sub	(_value);
         totalTokenBalances[_token] = totalTokenBalances[_token].sub	(_value);
         require(ERC20(_token).transfer(_to, _value));
         emit TokenSent(a.name, _token, _to, _value);
}
```

从一个帐户转移到另一个帐户，也不用担心更新余额或检查不一致:

```
function **transferETH**(Account storage _from, Account storage _to, uint _value) internal {
         require(_from.balanceETH >= _value);         	_from.balanceETH = _from.balanceETH.sub(_value);         	_to.balanceETH = _to.balanceETH.add(_value);
        emit ETHTransferred(_from.name, _to.name, _value);     
}function **transferToken**(Account storage _from, Account storage _to, address _token, uint _value) internal     
{         require(_from.tokenBalances[_token] >= _value);
         _from.tokenBalances[_token] = _from.tokenBalances	[_token].sub(_value);
         _to.tokenBalances[_token] = _to.tokenBalances[_token].add	(_value);
         emit TokenTransferred(_from.name, _to.name, _token, 	_value);     
}
```

有几种会计基础合同变化:

1.  简单记帐—与上述相同，但没有令牌功能
2.  会计——上面描述的那个
3.  津贴会计—有限帐户的所有会计+功能，这些帐户具有以每秒[天](/cryptolinks/maker-for-dummies-a-plain-english-explanation-of-the-dai-stablecoin-e4481d79b90)为单位的预设津贴。您可以将 Dai 或 ETH 存入有限的账户，当您提取 ETH 时，它会使用 MakerDAO 的价格反馈来检查汇率，并且在提取时只给您与您的津贴相等的 ETH 金额
4.  子帐户—允许拥有包含子帐户的超级帐户，以便可以清空超级帐户(子帐户的所有余额)，而无需更新子帐户。在您需要退还单笔金额或收集总计金额的情况下非常有用(例如，在众筹的情况下)。

# 示例:

## 存款箱

[DepositBox.sol](https://github.com/paradoxDAO/accounting/blob/master/examples/DepositBox.sol)

注意合同的可读性有多强——任何人都可以把钱存入自己的账户并提取。还要注意的是，合同的所有者可以将*剩余的*乙醚和代币存入基本账户，这样就不会再丢失代币了！

## 工资单

[Payroll.sol](https://github.com/paradoxDAO/accounting/blob/master/examples/Payroll.sol)

上面的例子将津贴会计用于一个非常简单的工资合同——资金可以存入个人账户，所有者可以为每个账户设置津贴。

## 资助活动

[RefundableCampaign.sol](https://github.com/paradoxDAO/accounting/blob/master/examples/RefundableCampaign.sol)

任何人都可以为筹资目标做出贡献。如果达到了目标，所有者会收集所有资金，但如果没有达到，每个人都会得到全额退款。

# 结论

我预计未来公司的大部分财务活动都将在以太坊区块链上进行，因此我们需要一种更好的方式来概念化账户和处理价值。我已经为任何金融相关合同提出了一个会计基础，以安全、简单和可读地处理以太和 ERC20 令牌。欢迎建议和投稿！