# 坚固性基础

> 原文：<https://medium.com/coinmonks/solidity-fundamentals-a71bf54c0b98?source=collection_archive---------3----------------------->

## 以太坊智能合约开发

## 数据位置和分配行为

![](img/a0e9c5141fd3d14675ae2b7356a9b700.png)

所有引用类型都有一个额外的注释，即*数据位置*，关于它存储的位置。有三个可能的选项:`memory`、`storage`和`calldata`。

`storage`:状态变量存储在区块链上的位置类型，这意味着具有`storage`位置的类型是持久的。

`memory`:变量在`memory`中，它们在函数调用过程中存在，这意味着得到这个位置的变量是临时的，在函数执行完成后，它们就不存在了。

`calldata`:存储函数参数的不可修改、非持久的数据位置，其行为很大程度上类似于`memory`数据位置，并且仅适用于`external`函数。关于函数类型(`external`、`public`、`internal`、`private`)的更多内容稍后再说。

> 更改数据位置的赋值或类型转换总是会导致自动复制操作，而同一数据位置内的赋值仅在某些情况下针对存储类型进行复制。

数据集位置不仅对数据的持久性很重要，对赋值的语义也很重要。让我们看看每个行为；

*   `storage`和`memory`(或来自`calldata`)之间的分配总是创建一个独立的副本。
*   从`memory`到`memory`的分配仅创建参考。因此，对一个内存变量的更改在引用相同数据的所有其他内存变量中也是可见的。
*   从`storage`到局部`storage`变量的赋值也只分配一个引用。
*   所有其他分配给`storage`的任务总是创建独立的副本。

我想提醒你一些关于结构用法的事情。让我们看看下面的例子；

关于引用类型的最后一点说明。如果我们处理引用类型，我们应该非常小心，原因如下。

*   引用类型不一定适合 32 字节— 256 位。
*   执行过程中消耗的气体量取决于数据位置。从引用类型创建独立的副本是很昂贵的，因此建议我们应该选择在函数内部使用内存数据位置。
*   当两个或更多不同变量指向同一数据位置时，我们必须小心，因为一个变量的任何变化都会影响其他变量。

最后，让我们看看在类似契约的契约中，结构、映射和枚举的一些复杂但非常有价值的用法。

接下来，我们将研究*控制结构*。感谢阅读。

**参考文献**

 [## 类型-坚固性 0.7.4 文件

### Solidity 是一种静态类型语言，这意味着每个变量(状态和局部)的类型都需要…

docs.soliditylang.org](https://docs.soliditylang.org/en/v0.7.4/types.html#reference-types) 

# 所有部件正常

*   [合同版面](https://ferdikurt.medium.com/solidity-fundamentals-88ceb4873064)
*   [值类型:第一部分](https://ferdikurt.medium.com/solidity-fundamentals-ca824a3ac023)
*   [值类型:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-c94460e3be3d)
*   [操作员](/coinmonks/solidity-fundamentals-1fb0e6b3b607)
*   [参考类型](/coinmonks/solidity-fundamentals-e4e4660e16c8)
*   [数据位置和赋值行为](/coinmonks/solidity-fundamentals-a71bf54c0b98)
*   [控制结构](/coinmonks/solidity-fundamentals-1dc2af4b453b)
*   [错误处理:断言、要求、恢复和异常](/coinmonks/solidity-fundamentals-a95bb6c8ba2a)
*   [功能:第一部分](/coinmonks/solidity-fundamentals-d2216be1c2c3)
*   [功能:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-functions-a7e3d38c1fe5)

随便问任何问题。

注意安全，做好工作，保持联系！

[费尔迪科特](https://www.linkedin.com/in/ferdi-kurt-8b910b164/)