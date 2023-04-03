# 坚固性基础:错误处理

> 原文：<https://medium.com/coinmonks/solidity-fundamentals-a95bb6c8ba2a?source=collection_archive---------0----------------------->

## 以太坊智能合约开发

## 断言、要求、恢复和异常

![](img/5c1b75d05d5da21124925c2a19bd9eeb.png)

在编写 *Solidity* 时，恢复状态变化以防止可能出现的问题是必要的。在这一部分中，我们将学习如何使用`revert`、`require`、`assert`和`try/catch`来处理错误，以撤销当前调用中对状态所做的所有更改。

目前，Solidity 支持两种错误签名:`Error(string)`和`Panic(uint256)`。`Error(string)`用于*常规的*错误条件，而`Panic(uint256)`用于无 bug 代码中不应该出现的错误。

**慌经** `**assert**`

`assert`应仅用于测试内部错误，以及检查不变量。它还应该返回 true，因为断言失败意味着代码中存在 bug。以下是*实性*创建**断言类型—** `Panic(uint256)` 异常的情况；

*   使用显示`false`的参数调用`assert`
*   提供的算术运算导致*下溢*或*上溢*(参见:[值类型:第一部分](/coinmonks/solidity-fundamentals-ca824a3ac023))
*   除以零或以零为模
*   将太大或负值转换为枚举
*   在空数组上调用`pop()`
*   访问越界或负数的数组元素
*   分配太多内存或创建太大的数组
*   调用内部函数类型的零初始化变量

**错误通过** `**require**`

`require`函数用于保证在执行前无法检测到的有效条件。它检查输入、*合同状态变量*的*条件或来自外部合同调用的*返回值*。它要么创建一个错误类型`Error(string)`，要么创建一个没有任何错误数据的错误。以下是 Solidity 创建**需求类型** — `Error(string)`异常的情况；*

*   使用显示 false 的参数调用 require
*   对不包含代码的协定执行外部函数调用
*   当契约通过不带 payable 修饰符的公共函数接收 Ether 时——包括构造函数和回退函数(稍后将详细介绍这些主题)
*   当你的契约通过一个**公共 getter** 函数获得以太时。

> ***记住:*** 你可以有选择地为`***require***`提供一个消息字符串，但不能为`***assert***`提供。

对于以下情况，将转发来自外部调用(如果提供)的错误数据。这意味着它可能会导致错误或混乱(或任何其他情况):

*   当`transfer()`失败时
*   当通过消息调用一个函数，但它没有正确完成时(即，它运行*耗尽*，没有*匹配的函数*，或*自身抛出异常*)，使用低级操作`call`、`send`、`delegatecall`、`callcode`或`staticcall`时除外。低级操作从不抛出异常，而是通过返回`false`来指示失败(稍后将详细介绍低级函数调用)。
*   当使用`new`关键字创建合同，但合同创建[未正确完成](https://docs.soliditylang.org/en/v0.8.0/control-structures.html?highlight=require#creating-contracts)时。

> ***注意:*** Panic 异常在 Solidity 0.8.0 之前曾经使用过`invalid`操作码，消耗了所有可供调用的 gas。使用`require`的例外用于消耗所有气体，直到 Metropolis 发布之前。

`**revert**`

为了恢复当前调用，我们可以使用*Solidity*函数来生成显示错误的异常。该函数将创建一个`Error(String)` 异常，该异常可选地接收包含错误详细信息的字符串消息。

如果我们直接提供错误字符串，那么以上两个语法选项是等价的，我们可以选择任何一个。

上例中，`revert(“Only exact payments!”)`返回以下十六进制作为错误返回数据:

`**try/catch**`

合同创建和外部调用期间的异常可以用`try` / `catch`语句捕获。`try`关键字后面必须跟一个外部函数调用或合同创建的表达式(`new ContractName()`)。

由`revert("reasonString")`或`**require**(false, "reasonString")`发生的错误是由类型`catch Error(string memory reason)`调用的 catch 子句导致的。

如果错误签名与任何其他子句都不匹配，如果解码错误消息时出现错误，或者如果异常没有提供错误数据，则执行`catch (bytes memory lowLevelData)`。

如果我们对错误数据不感兴趣，我们可以使用`catch { … }`。

为了捕捉所有的错误情况，我们至少要有子句`catch { ...}`或子句`catch (bytes memory lowLevelData) { ... }`。

接下来，我们将研究*实度函数类型* — `internal`、`external`、`public`和`private`。感谢阅读。

**参考文献**

 [## 错误处理:断言、要求、恢复和异常-可靠性 0.8.0

### 编辑描述

docs.soliditylang.org](https://docs.soliditylang.org/en/v0.8.0/control-structures.html?highlight=require#error-handling-assert-require-revert-and-exceptions) [](/coinmonks/solidity-fundamentals-ca824a3ac023) [## 坚固性基础

### 值类型:第一部分

medium.com](/coinmonks/solidity-fundamentals-ca824a3ac023)  [## 以身作则

### 使用 Solidity 学习智能合约编程

solidity-by-example.org](https://solidity-by-example.org) 

# 所有部件正常

*   [合同的版式](https://ferdikurt.medium.com/solidity-fundamentals-88ceb4873064)
*   [值类型:第一部分](https://ferdikurt.medium.com/solidity-fundamentals-ca824a3ac023)
*   [值类型:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-c94460e3be3d)
*   [操作员](/coinmonks/solidity-fundamentals-1fb0e6b3b607)
*   [参考类型](/coinmonks/solidity-fundamentals-e4e4660e16c8)
*   [数据位置和赋值行为](/coinmonks/solidity-fundamentals-a71bf54c0b98)
*   [控制结构](/coinmonks/solidity-fundamentals-1dc2af4b453b)
*   [错误处理:断言、要求、恢复和异常](/coinmonks/solidity-fundamentals-a95bb6c8ba2a)
*   [功能:第一部分](/coinmonks/solidity-fundamentals-d2216be1c2c3)
*   [功能:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-functions-a7e3d38c1fe5)

随意问任何问题。

**注意安全，做好工作，保持联系！**

[费尔迪科特](https://www.linkedin.com/in/ferdi-kurt-8b910b164/)