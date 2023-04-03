# 简单的 Vyper ERC 20 代币模板

> 原文：<https://medium.com/coinmonks/simple-vyper-erc-20-token-template-b60212b22662?source=collection_archive---------4----------------------->

用 Vyper 编写的非常简单的 ERC-20 智能合约模板，可以在以太坊区块链上创建自己的加密货币，有许多可定制的选项

GitHub:[https://GitHub . com/ayi double/Simple-Vyper-ERC-20-Token-Template](https://github.com/AYIDouble/Simple-Vyper-ERC-20-Token-Template)

***我想提醒读者，Vyper 仍在大量开发中，由于安全原因，代码可能在未来版本中无效。我会尽量用最新的 Vyper 版本更新代码。***

![](img/6498f994035e641b18f1c3136e6d7160.png)

Vyper Programming Language Logo

# 免责声明:

为了实现完全的可靠性兼容性，我们在 Vyper 中使用低级的 num256 数据类型。对于不要求完全坚固性兼容性的合同，或者不存在 num256 优于 num 的其他强制用法的合同，不推荐使用这种方法。值得注意的是，num256 没有溢出保护，并且随着语言的发展，可能不支持 num 的所有自动安全特性。正如这个令牌的代码所示，在 num 上使用 num256 还会使契约代码变得非常复杂。

**此代币未经审计，不建议用于生产资金。虽然作者已经尽了一切努力来确保所提供代码的安全性，但是资金损失总是有可能的，并且 Vyper 的编译器和语言仍然处于萌芽状态并在快速发展。**

# SmartContractCode.vy

**VYPER 仍在开发中，该代码不应用于生产**

```
# Solidity-Compatible EIP20/ERC20 Token
# Implements https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md# The use of the uint256 datatype as in this token is not
# recommended, as it can pose security risks.# This token is intended as a proof of concept towards
# language interoperability and not for production use.# Events issued by the contract
Transfer: event({_from: indexed(address), _to: indexed(address), _value: uint256(wei)})
Approval: event({_owner: indexed(address), _spender: indexed(address), _value: uint256(wei)})balances: uint256(wei)[address]
allowances: (uint256(wei)[address])[address]
num_issued: uint256(wei)@public
@payable
def deposit():
    _value: uint256(wei) = msg.value
    _sender: address = msg.sender
    self.balances[_sender] = self.balances[_sender] + _value
    self.num_issued = self.num_issued + _value
    # Fire deposit event as transfer from 0x0
    log.Transfer(0x0000000000000000000000000000000000000000, _sender, _value)@public
def withdraw(_value : uint256(wei)) -> bool:
    _sender: address = msg.sender
    # Make sure sufficient funds are present, op will not underflow supply
    # implicitly through overflow protection
    self.balances[_sender] = self.balances[_sender] - _value
    self.num_issued = self.num_issued - _value
    send(_sender, _value)
    # Fire withdraw event as transfer to 0x0
    log.Transfer(_sender, 0x0000000000000000000000000000000000000000, _value)
    return True@public
@constant
def totalSupply() -> uint256(wei):
    return self.num_issued@public
@constant
def balanceOf(_owner : address) -> uint256(wei):
    return self.balances[_owner]@public
def transfer(_to : address, _value : uint256(wei)) -> bool:
    _sender: address = msg.sender
    # Make sure sufficient funds are present implicitly through overflow protection
    self.balances[_sender] = self.balances[_sender] - _value
    self.balances[_to] = self.balances[_to] + _value
    # Fire transfer event
    log.Transfer(_sender, _to, _value)
    return True@public
def transferFrom(_from : address, _to : address, _value : uint256(wei)) -> bool:
    _sender: address = msg.sender
    allowance: uint256(wei) = self.allowances[_from][_sender]
    # Make sure sufficient funds/allowance are present implicitly through overflow protection
    self.balances[_from] = self.balances[_from] - _value
    self.balances[_to] = self.balances[_to] + _value
    self.allowances[_from][_sender] = allowance - _value
    # Fire transfer event
    log.Transfer(_from, _to, _value)
    return True@public
def approve(_spender : address, _value : uint256(wei)) -> bool:
    _sender: address = msg.sender
    self.allowances[_sender][_spender] = _value
    # Fire approval event
    log.Approval(_sender, _spender, _value)
    return True@public
@constant
def allowance(_owner : address, _spender : address) -> uint256(wei):
    return self.allowances[_owner][_spender]
```

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)