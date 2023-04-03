# 坚固性基础:功能

> 原文：<https://medium.com/coinmonks/solidity-fundamentals-d2216be1c2c3?source=collection_archive---------1----------------------->

## 以太坊智能合约开发

## 功能:第一部分

![](img/a7182e72eef9ab8c95ecc9c902e0dc81.png)

在这一部分中，我们将关注以下与**功能**相关的主题；

*   可见性和 Getters
*   功能修饰符
*   常量和不可变状态变量
*   函数参数和返回变量
*   纯视图函数

**可见性和 Getters**

*实度函数*和*状态变量*(参见:[数据位置和赋值行为](/coinmonks/solidity-fundamentals-a71bf54c0b98))有四种可见性:`external`、`public`、`internal`和`private`。`external`状态变量的可见性是不可能的。

`**external**` **:** 这些类型的功能只能从合同外部通过交易调用。不可能在内部调用`f() external`。当处理大型数据数组时，这些函数会非常高效，因为数据不会从`calldata`复制到`memory`(参见:[数据位置和分配行为](/coinmonks/solidity-fundamentals-a71bf54c0b98))。

可以从任何地方调用公共函数——在契约之外或者在内部。Getter 函数是为公共状态变量自动生成的。

`**internal**` **:** 这些函数和状态变量只能从当前契约或从其派生的契约中调用。

`**private**` **:** 私有函数和状态变量只能在定义它们的契约中调用。

> **注:**区块链之外的所有观察者都可以看到合同中的所有内容。制造某物`private`只是防止其他合同读取或修改信息，但是它仍然对区块链之外的整个世界可见。

**功能修饰符**

使用修饰符，我们可以在执行函数之前检查条件。它们是契约的可继承属性，可以被派生的契约覆盖，但前提是它们被标记为`virtual`(请等待后面章节中的 ***继承*** 章节)。

> **注意:**我们可以将多个修饰符应用到函数中，方法是将它们放在一个**空格分隔的**列表中，并按照给出的顺序对它们进行求值。

**常量和不可变状态变量**

`immutable`和`constant`是可以在状态变量上使用的关键字，以限制对其状态的修改。不同的是`constant`变量在编译后永远不能更改，而`immutable`变量可以在构造函数内设置。也可以在文件级声明`constant`变量。

> **注意:**编译器不会为这些变量保留**存储槽**，每次出现都会被相应的值所替换(参见:数据位置和赋值行为)。

> 目前，并非所有类型的常量和不变量都已实现。唯一支持的类型是[字符串](https://docs.soliditylang.org/en/v0.7.4/types.html#strings)(仅用于常量)和[值类型](https://docs.soliditylang.org/en/v0.7.4/types.html#value-types)。

**函数参数和返回变量**

函数可以接受类型化的参数并与其他语言进行比较，Solidity 函数也可以返回任意数量的值作为输出。

***功能参数***

*参数*的声明方式与变量相同。

> **注意:**一个[外部函数](https://docs.soliditylang.org/en/v0.7.4/control-structures.html#external-function-calls)不能接受多维数组作为输入参数。如果通过在源文件中添加`pragma experimental ABIEncoderV2;`来启用新的`ABIEncoderV2`特性，这个功能是可能的。
> 
> 一个[内部函数](https://docs.soliditylang.org/en/v0.7.4/control-structures.html#external-function-calls)可以接受一个多维数组而不启用该功能。

***返回变量***

*返回变量*使用与在`returns`关键字后声明参数相同的语法声明，它们可以用作任何其他局部变量。可以省略返回变量的名称。

> **注意:**不能从非内部函数返回某些类型，特别是多维动态数组和结构。如果您通过将`pragma experimental ABIEncoderV2;`添加到您的源文件来启用新的`ABIEncoderV2`特性，那么更多的类型是可用的，但是`mapping`类型仍然被限制在单个契约内，并且您不能转移它们。

**纯视图功能**

`**view**` **:** 功能可以声明视图，在这种情况下它们不修改状态。

以下语句被视为修改状态:

1.  写入状态变量。
2.  [发射事件](https://docs.soliditylang.org/en/v0.7.4/contracts.html#events)。
3.  [创建其他合同](https://docs.soliditylang.org/en/v0.7.4/control-structures.html#creating-contracts)。
4.  使用`selfdestruct`。
5.  通过电话发送以太网。
6.  调用任何未标记为`view`或`pure`的函数。
7.  使用低级调用。
8.  使用包含某些操作码的内联程序集。

> **注意:** Getter 方法被自动标记为`view`。

`**pure**` **:** 函数在不读取或修改状态时，可以声明为纯函数。

以下内容被视为从状态中读取:

1.  从状态变量中读取。
2.  访问`address(this).balance`或`<address>.balance`。
3.  访问`block`、`tx`、`msg`(除了`msg.sig`和`msg.data`)的任何成员。
4.  调用任何未标记的函数`pure`。
5.  使用包含某些操作码的内联程序集。

接下来我们将继续**函数:第二部分** — `*receive*` *以太函数*、`f*allback*` *函数*、*函数重载*和*事件*。感谢阅读。

**参考文献**

 [## 合同-可靠性 0.7.4 文件

### 编辑描述

docs.soliditylang.org](https://docs.soliditylang.org/en/v0.7.4/contracts.html#functions) [](/coinmonks/solidity-fundamentals-a71bf54c0b98) [## 可靠性基础|数据位置和赋值行为

### 数据位置和分配行为

数据位置和分配 Behaviormedium.com](/coinmonks/solidity-fundamentals-a71bf54c0b98) 

# 所有部件正常

*   [合同布局](https://ferdikurt.medium.com/solidity-fundamentals-88ceb4873064)
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

注意安全，做好工作，保持联系！

[费尔迪科特](https://www.linkedin.com/in/ferdi-kurt-8b910b164/)