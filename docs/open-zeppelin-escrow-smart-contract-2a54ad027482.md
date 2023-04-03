# 开放式齐柏林托管智能合同

> 原文：<https://medium.com/coinmonks/open-zeppelin-escrow-smart-contract-2a54ad027482?source=collection_archive---------3----------------------->

## 托管智能合同开放-齐柏林 2.0

![](img/974533390b3921ad7b0014aad397eecc.png)

[Source](https://www.google.co.in/url?sa=i&source=images&cd=&cad=rja&uact=8&ved=2ahUKEwin69zZma7eAhVL57wKHf-aCnIQjxx6BAgBEAI&url=https%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3DfZlHcazNMrc&psig=AOvVaw15UREEGFN2fIM7CpMCbQ8B&ust=1540990101846540)

今天，我们将看看智能契约托管，并了解 open-zeppelin 托管智能契约。

## 什么是托管？

[托管是一种金融安排，由第三方持有并监管特定交易中双方所需的资金支付。](https://www.escrow.com/what-is-escrow)托管被企业用作金融协议的受托方。有许多在线托管服务，我们也可以使用智能合同建立一个托管服务。

## 开放式齐柏林托管合同-

## 权限管理器智能合同(Secondary.sol)

在进入 open-zeppelin 托管智能合同之前，我们将首先看一下`Secondary.sol`，它也是由 open-zeppelin 提供的。它本质上是在`Escrow.sol`中使用的许可管理器包装器。

**为什么是 Secondary.sol？**

Secondary.sol 将有助于为我们的合同建立一个主要当事人。这个主要方是我们(托管服务)。这定义了在与我们的托管系统交互时将用于强制所有权的修饰符。当然，我们可以在托管智能合同本身中构建这一功能，但是创建`Secondary.sol`有助于我们实现可重用性、模块化和关注点分离。

**这里是**T3 的代码。

现在，让我们看看这份合同。

**建造方** -在区块链部署本合同的账户将是本智能合同的`_primary`(主要方)，并将控制本合同。

**修饰符-** 如果在任何函数中使用修饰符，将检查函数是否被主要方调用。

**转移所有权**——当然有改变主方的功能。

## 托管智能合同

因为我们提供托管服务。我们将控制我们的托管资金(我们将是第一方`Secondary.sol`)，我们将为一方存款和取款，并跟踪存款。任何人都可以查看给定地址的相应地址存入了多少金额。

好了，现在我们可以开始我们的托管智能合同了。那么现在我们来了解一下`Escrow.sol`。

我们正在定义一个映射来跟踪存款。它将地址与托管合同中的存款值进行映射。

```
mapping(address => uint256) private _deposits;
```

**事件**——智能合约还定义了两个事件，一个用于存款，一个用于取款。

```
event Deposited(address indexed payee, uint256 weiAmount);
event Withdrawn(address indexed payee, uint256 weiAmount);
```

**方法** -现在让我们看看我们在托管智能合同中的方法。

**押金** —这是一个简单的方法，只允许合同所有者(主要方)为任何一方提交押金。看，我们使用了来自`Secondary.sol`的`onlyPrimary`修饰符来进行权限管理。我们也存储我们存放乙醚的一方的地址。我们也在最后发射沉积。

```
function deposit(address payee) public onlyPrimary payable {
    uint256 amount = msg.value;
    _deposits[payee] = _deposits[payee].add(amount);

     emit Deposited(payee, amount);
  }
```

**撤回** —我们将使用此方法将乙醚转移回我们存放乙醚的一方。如果交易成功，我们将更新映射并发出一个事件。这里我们也使用`onlyPrimary`来确保只有我们可以执行撤销。

```
function withdraw(address payee) public onlyPrimary {
    uint256 payment = _deposits[payee]; _deposits[payee] = 0; payee.transfer(payment); emit Withdrawn(payee, payment);
  }
```

这是一个返回给定地址的存款值的函数，它是公共的，所以任何人都可以检查给定账户的托管余额。

```
function depositsOf(address payee) public view returns (uint256) {
    return _deposits[payee];
}
```

就这样，我们的智能合约完成了。我们可以在它的基础上添加额外的功能，它也经过了很好的测试，但是你可以测试它，只是为了了解它是如何工作的。我们将在接下来的教程中查看由 open-zeppelin library 提供的其他 2 个托管合同(有条件托管和退款),它们是基于这个托管智能合同构建的。

如果你对智能合约有什么不了解或者不了解，可以在下面评论。如果你觉得这有用，你可以👏在灵媒上跟着我。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)