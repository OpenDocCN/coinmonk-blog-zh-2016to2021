# 坚固性基础:类型

> 原文：<https://medium.com/coinmonks/solidity-fundamentals-e4e4660e16c8?source=collection_archive---------4----------------------->

## 以太坊智能合约开发

## 参考类型

![](img/3b4f72a4f22215783d2b114b9bb1378f.png)

在本部分中，我们将检查存储数据位置的**引用类型** — `arrays`、`struct`和`mapping` 、*引用*，并且不直接共享数据。很明显，我们应该更加小心地处理这些类型，因为我们正在处理位置，否则我们很容易失去性能。

**数组**是一组相同数据类型的元素，其中每个元素都有一个特定的位置，称为索引。数组的大小可以是固定的，也可以是动态的。

在 Solidity 中，`A[5]`总是一个包含五个`A`类型元素的数组，即使`A`本身就是一个数组。c 等其他语言就不是这样了。

数组有以下成员— `length`、`push()`、`push(x)`、`pop()`。让我们用一些例子来看看它们中的每一个；

Solidity 中的**struct**允许以 struct 的形式声明新的类型。这种类型是一组不同的类型，可以包含值类型和引用类型。有一个必要的限制—结构不可能包含其自身类型的成员，因为结构的大小必须是有限的。这并不意味着 struct 不能包含 struct，例如`struct A`可以包含`struct B`但是`struct A` 不能包含自己的类型`struct A`。

**映射**在一个键-值对中存储数据，其中一个键可以是任何值类型。我们可以认为这种引用类型类似于任何其他编程语言中的哈希表或字典，可以通过键来检索数据。

**结构**与**映射**和**数组**配合得非常好，但是乍一看，要习惯它有点棘手。

所有引用类型都有一个额外的注释，即*数据位置*，关于它存储的位置。有三个可能的选项:`memory`、`storage`和`calldata`。但是我们将在下一篇文章中查看 ***数据位置和赋值*** **行为** 。

**参考文献**

 [## 类型-坚固性 0.7.5 文件

### Solidity 是一种静态类型语言，这意味着每个变量(状态和局部)的类型都需要…

docs.soliditylang.org](https://docs.soliditylang.org/en/v0.7.5/types.html#reference-types) 

# 所有部件正常

*   [合同的布局](https://ferdikurt.medium.com/solidity-fundamentals-88ceb4873064)
*   [值类型:第一部分](https://ferdikurt.medium.com/solidity-fundamentals-ca824a3ac023)
*   [值类型:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-c94460e3be3d)
*   [运算符](/coinmonks/solidity-fundamentals-1fb0e6b3b607)
*   [参考类型](/coinmonks/solidity-fundamentals-e4e4660e16c8)
*   [数据位置和赋值行为](/coinmonks/solidity-fundamentals-a71bf54c0b98)
*   [控制结构](/coinmonks/solidity-fundamentals-1dc2af4b453b)
*   [错误处理:断言、要求、恢复和异常](/coinmonks/solidity-fundamentals-a95bb6c8ba2a)
*   [功能:第一部分](/coinmonks/solidity-fundamentals-d2216be1c2c3)
*   [功能:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-functions-a7e3d38c1fe5)

随便问任何问题。

注意安全，做好工作，保持联系！

[费尔迪科特](https://www.linkedin.com/in/ferdi-kurt-8b910b164/)