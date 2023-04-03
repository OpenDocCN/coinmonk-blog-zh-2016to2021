# 学扎实。泳池里的鲨鱼——第一部分

> 原文：<https://medium.com/coinmonks/learn-solidity-shark-of-the-pool-part-1-ac0f733eecdd?source=collection_archive---------0----------------------->

![](img/9bbc01aafa66a981be17676180d9b188.png)

[Image source](https://www.pexels.com/photo/code-coding-computer-data-574070/)

在本系列中，我们将使用 Solidity smart contracts 基于以太坊区块链创建一个简单的 Dapp。在后面的部分中，我们将看到如何测试和部署契约。我们还将创建 React Web 应用程序，并使用 MetaMask 进行区块链交易，以获得功能齐全的 Dapp。

在[这个地址](http://34.210.217.34/)上可以获得 Dapp 的演示。

所有的**源代码**都可以在[这个 GitHub 库](https://github.com/joze144/pool-shark)中免费获得。

加入我们: [Solidity 开发社区，了解更多信息](https://solidity_devs.select.id/bjxx)

> [发现并回顾最佳区块链软件](https://coincodecap.com)

# Dapp 的目标

我把这个 Dapp 命名为“泳池鲨鱼”，但是它到底是做什么的呢？Dapp 的目标很简单，我希望能够创建一个游泳池，人们可以贡献他们宝贵的以太。收款期是有期限的，超过期限就不能再往池子里存款了。

当设定的截止日期到期时，我们希望看到谁是池中的鲨鱼，或最大的贡献者。那个地址的所有者，我们将称他为鲨鱼，是赢家，可以提取所有收集的资金。

# 智能合同

让我们直接进入正题。我将假设读者不是第一次接触 Solidity，并且可以跟随我完成开发。否则， [Solidity documentation](https://solidity.readthedocs.io/) 就是你的朋友。我也强烈推荐使用[松露框架](http://truffleframework.com/)和浏览[他们的教程](http://truffleframework.com/tutorials/)。

目标看起来很简单，但是当你开始从发展的角度考虑时，它可能不是那么简单。我们需要的是把逻辑分成几个模块。

## 联营合同

Pool 将完全按照我们前面描述的那样工作。池合同接受存款，并在收款期间安全地存储它们。截止日期后，它拒绝任何进一步的存款，并允许为池中的鲨鱼提取资金。

让我们首先定义接口。接口是 Solidity 开发的重要部分。为了更好地了解为什么你应该使用它们，我推荐[这篇由](https://blog.colony.io/writing-upgradeable-contracts-in-solidity-6743f0eecc88)[埃琳娜·迪米特罗娃](https://medium.com/u/899aa52b377e?source=post_page-----ac0f733eecdd--------------------------------)撰写的精彩文章。

```
**pragma** solidity ^0.4.21;

**contract** iPool {
    **function** () **public payable**;

    **function** withdraw() **public returns** (**bool** success);
}
```

但是你可能会问，我们没有忘记存款功能吗？定金是合同的直接交易。它在无名称函数中处理。不要忘记**应付款**关键字，允许函数接收以太的函数修饰符。[关于主题](/@kidinamoto/payable-keyword-in-solidity-375b5c9044df)的更多信息，作者 [Sophie Wong](https://medium.com/u/bdde9b38adcd?source=post_page-----ac0f733eecdd--------------------------------) 。

我们希望存款和取款功能有时间限制。为此，我们将在定时契约中为函数创建[修饰符。它们在被修改的函数执行之前被执行，是放置函数执行约束的好地方。](http://solidity.readthedocs.io/en/v0.4.21/contracts.html#function-modifiers)

```
**pragma** solidity ^0.4.21;

**contract** Timed {

    **uint256 public** deadline;

    **modifier** onlyWhileOpen {
        require(block.timestamp <= deadline);
        _;
    }

    **modifier** onlyWhileClosed {
        require(block.timestamp > deadline);
        _;
    }
}
```

我们在这里使用了一些奇特的东西，所以让我们解释一下:

*   **block.timestamp** 以历元秒为单位提供当前块时间
*   **修改器**创建用于功能的修改器
*   **_；**此语句后，修改功能的工作流程继续

现在让我们进入池实现部分。首先让我们创建构造函数

```
**contract** Pool **is** iPool, Timed {
    **using** SafeMath **for uint256**;

    **string public** name;
    **address public** token;
    **uint256 public** rate;

    **function** Pool(**string** _name, **uint256** _rate, **uint256** _deadline) **public** {
        require(_rate > 0);
        require(_deadline > block.timestamp);
        name = _name;
        rate = _rate;
        deadline = _deadline;
        token = **new** FishToken(_deadline);
    }
```

构造函数创建将与该池关联的 FishToken 实例。FishToken 将用于存款跟踪和鲨鱼选择。

我们必须在调用构造函数时做一些检查。截止日期必须大于当前时间，奖励分配率必须大于 0。

让我们看看存款功能

```
**function** () **public payable** onlyWhileOpen {
    require(msg.value > 0);
    **uint256** rewardTokens = rate.mul(msg.value);
    require(iFishToken(token).issueTokens(msg.sender, rewardTokens));
}
```

这么简单对吧？实际上，它所做的只是根据设定的汇率计算收到的令牌，并触发 FishToken 合约上的发行功能。现在我们可以看到如何应用函数修饰符。 **onlyWhileOpen** 修饰符将在函数执行之前执行，如果时间超过截止日期，将抛出错误。

和我们的取款功能

```
**function** withdraw() **public** onlyWhileClosed **returns** (**bool** success) {
    **if**(iFishToken(token).isShark(msg.sender)) {
       msg.sender.transfer(**address**(this).balance);
        **return true**;
    }
    **return false**;
}
```

撤回功能仅在关闭修改器时由**修改。它还执行检查，如果发送者想要撤回是真正的鲨鱼。如果是的话，他会得到泳池合同的所有乙醚。幸运的家伙！**

这就是泳池合同，它有足够多的逻辑，我们不想进一步复杂化的事情。让我们进入最复杂的部分，我们的鱼叉合同。

## 鱼类代币合同

现在，我们想记录乙醚的贡献，并确定鲨鱼。这是 Dapp 中最复杂的部分，所以如果看起来逻辑不清楚，不要绝望。我首先使用了一个标准的令牌契约，并做了很多修改以适应这个 Dapp 的需要。

我将合同命名为 FishToken，因为它跟踪池中的鱼，并确定其中的鲨鱼。

因此，让我们为这个契约定义一个接口

```
**contract** iFishToken {

    **function** balanceOf(**address** _owner) **public view returns** (**uint256** balance);

    **function** transfer(**address** _to, **uint256** _amount) **public returns** (**bool** success);

    **function** issueTokens(**address** _beneficiary, **uint256** _amount) **public returns** (**bool** success);

    **function** getShark() **public view returns** (**address** sharkAddress, **uint256** sharkBalance);

    **function** isShark(**address** _address) **public view returns** (**bool** success);

    */// @notice Event propagated on every executed transaction* **event** LogTransfer(**address** indexed _from, **address** indexed _to, **uint256** _value);

    */// @notice Event propagated when new deposit is made to the pool* **event** LogIssue(**address** indexed _member, **uint256** _value);

    */// @notice Event propagated when new address has the most tokens* **event** LogNewShark(**address** indexed _shark, **uint256** _value);
}
```

我知道，太多了。让我们过一遍。构造函数的首次实现

```
**contract** FishToken **is** iFishToken, Ownable, Timed {
    **using** SafeMath **for uint256**;

    **uint8 public** decimals;                *//How many decimals to show* **address public** currentShark;
    **uint256 public** totalSupply;
    **mapping**(**address** => **uint256**) **public** balances;

    **mapping**(**address** => **bool**) **public** participantsMap;
    **address**[] **public** participantsArray;

    **function** FishToken(**uint256** _deadline) **public** {
        deadline = _deadline;
        totalSupply = 0;
        currentShark = msg.sender;
        owner = msg.sender;
    }
```

函数 **balanceOf()** 和 **transfer()** 类似于经典的令牌函数。

```
**function** transfer(**address** _to, **uint256** _value) **public** onlyWhileOpen **returns** (**bool** success) {
    **if** (balances[msg.sender] < _value || balances[_to] + _value <= balances[_to]) {
        **return false**;
    }
    addToParticipants(_to);
    balances[msg.sender] = balances[msg.sender].sub(_value);
    balances[_to] = balances[_to].add(_value);

    emit LogTransfer(msg.sender, _to, _value);

    determineNewShark();

    **return true**;
}**function** balanceOf(**address** _owner) **public view returns** (**uint256** balance) {
    **return** balances[_owner];
}
```

他们所做的是使参与者之间的奖励令牌转移，并检查当前余额。仅当打开修饰符时，才使用**修改转移，这意味着转移仅在收集期内是可能的。这样，如果从多个地址投稿，用户可以聚合收到的令牌。或者给另一个人代币。**

我们使用 zeppelin-solidity 库中的 [SafeMath 库进行数学运算。此外，当事务发生时，我们会发出日志事件。事件是与客户端应用程序进行交互的一种很好的方式。您可以创建侦听器并侦听协定上发出的事件。这样，当事情发生时，您可以获得推送事件，而不需要定期查询合同。](https://github.com/OpenZeppelin/zeppelin-solidity/blob/master/contracts/math/SafeMath.sol)

函数 **addToParticipants()** 和 **determineNewShark()** ，用于鲨鱼选择。每当令牌分发发生变化时，我们都需要找到新的 shark。

接下来是函数 **issueTokens()**

```
**function** issueTokens(**address** _beneficiary, **uint256** _amount) **public** onlyOwner onlyWhileOpen **returns** (**bool** success) {
    **if**(balances[_beneficiary] + _amount <= balances[_beneficiary]) {
        **return false**;
    }
    addToParticipants(_beneficiary);
    balances[_beneficiary] = _amount.add(balances[_beneficiary]);
    totalSupply = _amount.add(totalSupply);

    emit LogIssue(_beneficiary, _amount);

    determineNewShark();

    **return true**;
}
```

函数 **issueToken()** 由池合同触发。因为我们使用池契约创建了 FishToken 契约，所以该契约成为所有者。用 **onlyOwner** 修饰符修改 **issueTokens()** ，makes 仅在从创建 FishToken 契约的池契约调用时执行。这意味着只有母池合同可以发行新令牌。

对于 Ownable contract，我使用了来自 zeppelin-solidity 仓库的那个。他们有很多合同，你可以在自己的 Dapps 中使用。

发行代币非常简单。我们增加了地址和总供给的平衡。我们必须再次调用函数 **addToParticipants()** 和 **determineNewShark()** ，因为令牌分发的状态发生了变化，我们必须检查 Shark 是否发生了变化。

我们还发出事件**logisue()**，这将有助于我们使用客户端应用程序。

## 确定鲨鱼

该函数驻留在 FishToken 合同中。该算法的基本思想是，记录所有可能有令牌的地址。这是在 participantsArray 中完成的。为了避免在**分词短语**中出现双重插入，我们还增加了**分词短语**，对于添加到数组中的地址，该短语被设置为 true。

要确定鲨鱼，我们只需循环通过 **participantsArray** 并将令牌最多的一个设置为鲨鱼。

```
**mapping**(**address** => **bool**) **public** participantsMap;
**address**[] **public** participantsArray;**function** determineNewShark() **internal** {
    **address** shark = participantsArray[0];
    **uint** arrayLength = participantsArray.length;
    **for** (**uint** i=1; i < arrayLength; i++) {
        **if** (balances[shark] < balances[participantsArray[i]]) {
            shark = participantsArray[i];
        }
    }

    **if**(currentShark != shark) {
        currentShark = shark;
        emit LogNewShark(shark, balances[shark]);
    }
}

**function** addToParticipants(**address** _address) **internal returns** (**bool** success) {
    **if**(participantsMap[_address]) {
        **return false**;
    }
    participantsMap[_address] = **true**;
    participantsArray.push(_address);
    **return true**;
}
```

这些功能被设置为内部功能，这意味着它们只能由合同中的另一个功能触发，而不能作为合同上的一项交易。

每当令牌分发的状态改变时，我们必须确定新的 Shark。这意味着我们必须对每次交易或令牌发放进行重新计算。

# **结论**

开始吧，我们已经创建了一个功能齐全的 Dapp，它可以跟踪 Ether 对池的贡献，并确定 Shark 是其中最大的贡献者。我们已经启用了地址间奖励令牌的转移，这意味着您可以和一个朋友一起参与，也可以使用多个地址。

你在我的代码中发现任何错误吗？您看到优化它的方法了吗？请让我知道，我很想听听你的反馈！

**Dapp 演示版**可在[这个地址](http://34.210.217.34/)上获得。

所有**源代码**都可以在[这个 GitHub 库中](https://github.com/joze144/pool-shark)免费获得。

## 该系列的部分:

*   **第一部分-稳健智能合同**
*   [第 2 部分-检测稠度 Dapp](/@jozhe/testing-solidity-dapp-sotp-part-2-9685b3375aaf)
*   [第 3 部分-淋巴结。Solidity Dapp 的 JS 后端服务](/@jozhe/node-js-backend-service-for-ethereum-dapp-sotp-part-3-2d3aa5ec50e9)
*   [第 4 部分-让 Web 应用与元掩码 Web3 反应](/@jozhe/react-web-dapp-with-metamask-web3-sotp-part-4-f252ebe8d07f)

[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)