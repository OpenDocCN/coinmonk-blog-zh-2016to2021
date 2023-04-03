# 可更新的智能合约:消除信任需求

> 原文：<https://medium.com/coinmonks/updatable-smart-contracts-eliminating-need-for-trust-2e1925fc6d17?source=collection_archive---------7----------------------->

许多开发人员在智能合同方面的一个关键问题是:一旦它们被部署，它们就在那里！在正常情况下，部署后更改合同极其困难；不是为了操纵，也不是为了进步。这就是为什么开发人员开始考虑设计一个智能合同后端的方法，该后端可以按照合同所有者认为合适的方式进行更新。

![](img/7d8109f827ea139615b7dc21ecd4d019.png)

Photo by [rawpixel](https://unsplash.com/@rawpixel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 合同结构

简单总结一下最常见的可更新契约的设计:

可更新性可以通过从函数中分离变量来实现，这意味着有一个*核心*契约，包含所有变量和任意数量的*子*契约，其中将定义用于修改来自*核心*契约的变量的函数。为了确保只有少数选择的契约可以调用核心契约中的 setter，将会有一个可信地址列表，这些地址可以调用各自的 setter 函数。这些受信任的*子*契约可以在将来被销毁和替换，这样整个契约基本上都可以被重写——甚至在部署之后。

如果你不熟悉这种设计，可以看看下面的帖子来获得更详细的描述。

*   [https://medium . com/rocket-pool/upgradable-solidity-contracts-design-54789205276d](/rocket-pool/upgradable-solidity-contracts-design-54789205276d)
*   [https://blog . colony . io/writing-upgradable-contracts-in-solidity-6743 f 0 eecc 88](https://blog.colony.io/writing-upgradable-contracts-in-solidity-6743f0eecc88)
*   [https://blog.indorse..io/以太坊-可升级-智能-合同-策略-456350d0557c](https://blog.indorse..io/ethereum-upgradable-smart-contract-strategies-456350d0557c)

## 信任问题

如前所述，一方面，智能合同的这一特征限制了合同进展的可能性，另一方面，它彻底消除了对合同所有者信任的需要，如果有的话。

换句话说，如果我们使我们的合同完全可更新，我们就完全有权对我们的合同做任何事情；包括偷代币、资金等。这与最初的原因有点矛盾，为什么逻辑被移到了区块链。

下面是这种操作的一个例子:

假设您在链上创建了一个简单的 ERC20 令牌，其设计如前所述。这意味着您的*核心*合同包括:

**变量 *:***

```
mapping (address => uint256) public balanceOf;
mapping (address => mapping (address => uint256)) allowance;uint256 public totalSupply;
string public name;
string public symbol;
```

**设定器:**

```
function setBalanceOf(address _from, uint256 _value) public childContractOnly { balanceOf[_from] = _value;}function setAllowance(address _from, address _spender, uint256 _value) public childContractOnly { allowance[_from][_spender] = _value;}...
```

我们现在可以做的是创建一个契约，将它添加到核心契约的可信列表中，然后在另一个函数中调用 T2 的 setBalanceOf 函数，我们将这个函数限制为只能通过 T4 的契约所有者 T5 调用。这样，举例来说，如果这些代币由乙醚支持，我们可以从合同中窃取全部资金。

```
function setBalanceOf(address _from, uint256 _value) public contractOwnerOnly { core.setBalanceOf(_from, _value);}
```

所有这些都可以在短时间内完成，所以当用户得到操纵的风声时，肯定已经太晚了…

## 消除信任需求:批准支持的更新

这里想到的第一个想法是引入一个投票合同，该合同将成为*合同所有者*，并需要大多数“股东”的批准，以便能够进行更新。在我们的例子中，看看上面的例子，您还可以让余额充当投票的权利，以接受或拒绝提议的更新。然而，这里的问题是代币的可替代性。为了防止某个特定令牌的重复投票，用户必须在投票期间锁定他们的令牌。这将导致选民投票率下降，因此是一个相当不方便的解决方案。

与其让用户投票决定是接受还是拒绝一个更新，一个更好的方法是在*核心*契约中包含一个映射，跟踪哪些用户已经接受新契约为“有效”，然后只允许相应的*子*契约调用这些帐户的设置者。

```
mapping (address => mapping (address => uint256)) public approved;function approve(address _contract) public { approved[_contract][msg.sender] = 1;}
```

嗯，实际上这并不是我们问题的完整解决方案，因为上面的循环孔仍然可以由*合同所有者*构成。它所保护的是用户的余额在未经他们允许的情况下从国外合约中减少。为了完成这一设计，我们将向 *approved* 添加一个余额，该余额将跟踪已被相应子合同删除的所有令牌，然后只允许它再次重新创建这个确切的金额。

这是它的实现方式:

```
function setBalanceOf(address _from, uint256 _value, bool _add) public childContractOnly { require(approved[msg.sender][_from] == 1); if(_add) { require(approved[msg.sender][msg.sender] >= _value); approved[msg.sender][msg.sender] -= _value;
        balanceOf[_from] += _value;
        totalSupply += _value; } else { require(balanceOf[msg.sender] >= _value);
        balanceOf[_from] -= _value;
        totalSupply -= _value;
        approved[msg.sender][msg.sender] += _value; }}
```

现在，这也可以扩展到多个变量。然而，这需要对变量 *approved* 的声明和使用稍作修改:

```
mapping (address => mapping (bytes32 => uint256)) public approved;function setBalanceOf(address _from, uint256 _value, bool _add) public childContractOnly { require(approved[msg.sender][_from] == 1);
    bytes32 key = keccak256(msg.sender, "setBalanceOf") if(_add) { require(approved[msg.sender][key] >= _value); approved[msg.sender][msg.sender] -= _value;
        balanceOf[_from] += _value;
        totalSupply += _value; } else { require(balanceOf[msg.sender] >= _value);
        balanceOf[_from] -= _value;
        totalSupply -= _value;
        approved[msg.sender][key] += _value; }}
```

这一改变背后的意图是能够将子契约的地址与变量名散列在一起，您将存储消除的总量，以便获得唯一的键。我强烈建议您仅在需要管理不止一个非相关变量的情况下使用这种方法，因为这种设计会因为哈希过程而消耗更多的能量。

上述实现可以用于任何可替换的令牌。然而，设计原则本身也适用于不可替换的令牌。不同之处在于，必须有一个新的映射来表明某个特定的令牌是否已经被一个子契约获取，而不是在*批准的*映射中的余额。

## 总结

就是这样！现在，您拥有了一个完全可更新的合同结构，而您的用户可以平静地使用您的合同，而不必担心您是否忠实。您可能已经注意到，与最初的设计相比，这种设计会稍微贵一点，因为我们额外存储了到目前为止合同中已经取消的令牌数量。尽管如此，如果您希望创建一个处理大量值的契约，并且仍然希望它是可更新的，那么这里介绍的方法至少是值得考虑的。