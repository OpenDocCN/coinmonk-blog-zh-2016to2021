# 给 Vyper 初学者的提示

> 原文：<https://medium.com/coinmonks/for-those-who-is-going-to-learn-vyper-8bac3cc0e01c?source=collection_archive---------2----------------------->

![](img/29ef044bee4c862ca5d847220c260a85.png)

## 有些事情你应该记住

1.  Vyper 不是 Solidity 的替代品，但它提供了一些安全、简单的高级功能。
2.  Vyper 不提供动态类型、类继承、函数重载、运算符重载以及递归、修饰符、内联汇编等

## Vyper 哲学

1.  安全性:在 Vyper 中构建安全的智能合约应该是可能的，也是自然的。
2.  **简单性** : Vyper 的目标是构建一种外行人也容易理解和审核的编程语言。对读者来说简单比对作者来说简单更重要，而对没有多少 Vyper 使用经验(以及一般编程经验)的读者来说简单尤为重要。

> [**阅读更多关于 Vyper 编程语言的文章**](/coinmonks/search?q=vyper)

# 安装、语法和示例的基础

Vyper 现在仍处于实验开发阶段，而[正式文档](https://vyper.readthedocs.io/en/latest/)是您应该首先开始的地方。之后，请遵循下面提供的列表:

1.  [工具和资源](https://github.com/vyperlang/vyper/wiki/Vyper-tools-and-resources)
2.  [Gitter 频道](https://gitter.im/ethereum/vyper)获取快速帮助和讨论
3.  [从零开始快速学习 Vyper】](https://learnxinyminutes.com/docs/vyper/)
4.  除了来自官方文件的例子外，LayerXverified Vyper smart contract 是一个阅读和实践的好地方

## 调用外部合同

在智能契约开发中，我们经常调用外部契约来获得状态或执行。在 Solidity 上，在互联网上走来走去是相当容易的。

然而在 Vyper 中，一些特殊情况下的信息仍然很少，这就是我写这篇文章的原因。

在 Vyper 中有两种方法可以做到这一点:

## 声明契约接口并调用 Solidity:

接口可以通过内联定义或从单独的文件导入来添加到智能合约中。

此外，Vyper 还有内置接口 **ERC20** 、 **ERC721、**你可以用:

```
from vyper.interfaces import ERC20
```

要声明一个接口，使用如下的**契约**关键字:

```
# Declare contract interface FooBar with 2 function
contract FooBar:
    **def** calculate() -> uint256: constant # get state of FooBar contract
    **def** test1(): modifying # A call will execute and change state of FooBar contract# Function make external call
@public
def makeCall(fooBarAddress: address):
    FooBar(fooBarAddress).calculate()  *# cannot change storage*
    FooBar(fooBarAddress).test1()  *# storage can be altered*
```

或者您可以使用 **raw_call** 调用另一个契约，这是一个低级内置函数，提供对指定的[以太坊](https://blog.coincodecap.com/tag/ethereum/)地址的调用，带有函数签名和数据、值。

由于 Vyper 不提供动态类型，这意味着如果你调用一个动态类型的函数，如 **uint256[]，raw_call** 是唯一的方法。

例如，你有一个像[这样的函数:](https://github.com/CryptoManiacsZone/1split/blob/master/contracts/IOneSplit.sol)

```
function swap(address fromToken, address toToken, uint256 amount,        uint256 minReturn, uint256[] memory distribution, uint256 disableFlags) public payable;
```

您**不能**像下面的代码一样在 Vyper 中声明接口，因为 Vyper 不提供动态类型:

```
contract IOneSplit:
   **def** swap(fromToken: address, toToken: address, amount: uint256, minReturn: uint256, distribution: uint256[], disableFlags: uint256) -> uint256: modifying
```

或者甚至你知道**分布的大小(见下面的代码)**并写一个接口代码如:

```
contract IOneSplit:
   **def** swap(fromToken: address, toToken: address, amount: uint256, minReturn: uint256, distribution: uint256[4], disableFlags: uint256) -> uint256: modifying
```

上述代码是正确的 Vyper 语法，但它不是 **OneSplit 的 **swap** 函数，因为 **uint256[]** 和 **uint256[4]之间的函数签名会不同。****

## **迄今为止唯一正确的方法……**

因此，从 Vyper 调用 Solidity contract 中具有动态参数的函数的唯一方法是使用具有 4 字节签名和数据的 **raw_call** 函数进行调用，值如下:

Raw_call example

**funSig** 变量是 **OneSplit 的交换函数**和 **data 的函数签名:bytes[352]** 是 352 字节的数据将作为参数传递到该函数中。

# 参考资料:

1.  [https://github . com/vyperlang/Vyper/wiki/Vyper-tools-and-resources](https://github.com/vyperlang/vyper/wiki/Vyper-tools-and-resources)
2.  [https://vyper.readthedocs.io/en/latest/index.html](https://vyper.readthedocs.io/en/latest/index.html)
3.  [https://learnxinyminutes.com/docs/vyper/](https://learnxinyminutes.com/docs/vyper/)
4.  [https://github . com/cryptomaniaczone/1 split/tree/master/contracts](https://github.com/CryptoManiacsZone/1split/tree/master/contracts)
5.  [https://blockgeeks.com/guides/understanding-vyper/](https://blockgeeks.com/guides/understanding-vyper/)

![](img/e9dbce386c4f90837b5db529a4c87766.png)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)