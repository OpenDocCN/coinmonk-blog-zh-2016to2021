# 坚固性基础

> 原文：<https://medium.com/coinmonks/solidity-fundamentals-1fb0e6b3b607?source=collection_archive---------1----------------------->

## 以太坊智能合约开发

## 运算符:算术、逻辑、比较、赋值、按位、条件

![](img/29c045e003b8d9c8281732f57d5f3cfa.png)

在这一节中，我们将学习在任何编程语言中都起着至关重要作用的运算符。同时， *Solidity* 中使用的**运算符**与 *Javascript* 非常相似。我们可以将它们分为 6 个主要类别:

1.  算术运算符
2.  逻辑运算符
3.  比较运算符
4.  赋值运算符
5.  按位运算符
6.  条件运算符

**算术运算符**

这些运算符用于执行算术-数学运算。

**逻辑运算符**

组合两个或多个条件需要使用逻辑运算符。

**比较运算符**

这些运算符用于比较两个值。

**赋值运算符**

为了给变量赋值，solidity 支持下面的赋值操作符。

**按位运算符**

这些运算符用于执行位级运算，如`AND`、`OR`、`XOR`、`NOT`等。

**条件运算符**

**条件(三元)操作符**是一个带三个操作数的操作符:一个条件后跟一个问号(`?`)，如果条件为真则执行一个表达式，后跟一个冒号(`:`)，最后是如果条件为假则执行的表达式。该操作符经常被用作`if`语句的快捷方式。

应该仔细检查无符号数学运算。有些情况需要注意，因为它们可能会在生产中造成混乱，例如，两个无符号整数的乘法运算可能会导致溢出，或者两个无符号整数的减法运算可能会导致下溢。

在稍后的合同安全性章节中，我们将使用开源库来处理这类场景，比如 Open Zeppelin 的 SafeMath。

在下一部分中，我们将研究**引用类型**，比如数组、结构和映射。感谢阅读。

**参考文献**

 [## 坚固性-坚固性 0.7.5 文件

### Solidity 是一种面向对象的高级语言，用于实现智能合约。智能合同是程序…

docs.soliditylang.org](https://docs.soliditylang.org/en/v0.7.5/index.html)  [## 以太坊智能合约最佳实践

### 本文档为中级 Solidity 程序员提供了安全考虑的基本知识。这是…

consensys.github.io](https://consensys.github.io/smart-contract-best-practices/) 

# 所有部件正常

*   [合同的版式](https://ferdikurt.medium.com/solidity-fundamentals-88ceb4873064)
*   [值类型:第一部分](https://ferdikurt.medium.com/solidity-fundamentals-ca824a3ac023)
*   [值类型:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-c94460e3be3d)
*   [操作员](/coinmonks/solidity-fundamentals-1fb0e6b3b607)
*   [参考类型](/coinmonks/solidity-fundamentals-e4e4660e16c8)
*   [数据位置和分配行为](/coinmonks/solidity-fundamentals-a71bf54c0b98)
*   [控制结构](/coinmonks/solidity-fundamentals-1dc2af4b453b)
*   [错误处理:断言、要求、恢复和异常](/coinmonks/solidity-fundamentals-a95bb6c8ba2a)
*   [功能:第一部分](/coinmonks/solidity-fundamentals-d2216be1c2c3)
*   [功能:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-functions-a7e3d38c1fe5)

随便问任何问题。

注意安全，做好工作，保持联系！

[费尔迪科特](https://www.linkedin.com/in/ferdi-kurt-8b910b164/)