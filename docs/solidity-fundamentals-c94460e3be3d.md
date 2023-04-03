# 坚固性基础:类型

> 原文：<https://medium.com/coinmonks/solidity-fundamentals-c94460e3be3d?source=collection_archive---------4----------------------->

## 以太坊智能合约开发

## 值类型:第二部分

![](img/5c563859df9953716c0c7a682301f6c2.png)

在这一部分中，我们将会看到下面的**值类型**；

*   固定大小的字节数组
*   动态大小的值类型(字节、字符串)
*   枚举

**固定大小的字节数组** —字节 1 (byte)，字节 2 …字节 32

值类型`bytes1`、`bytes2`、…、`bytes32`包括从 1 到 32 的字节序列。关键字`byte`是`byte1`的别名。此外，固定大小的值类型有一个名为`length`的成员函数，它产生固定长度的字节数组(只读)。并且带有固定大小变量的`bytes`也可以在契约之间传递。

> 类型`byte[]`是一个字节数组，但是由于填充规则，它为每个元素浪费了 31 个字节的空间(存储中除外)。最好用`bytes`型代替。

我们使用十六进制表示来初始化它们:

```
bytes1 a = 0x15; // [00010101]
bytes1 b = 0xf6; // [11110110]
```

**动态调整大小的值类型(字节、字符串)**

类型`bytes`和`string`的变量是实性中的特殊数组。

**字节**

`bytes`用于任意长度的原始字节数据。`bytes`长度从 1 到 32 不等，在坚固性上被视为一个阵列。我们可以*推动*，*弹出*并得到`bytes`的*长度*。

**琴弦**

`string`用于存储等于或大于一个字节的任意长度的字符集(UTF-8)。它有一个动态长度，所以不能在契约之间传递。此外，它不允许长度或索引访问。

> `*string*` *不等于* `*bytes32*` *但等于* `*bytes*` *，因为它的长度是动态的。*

为了更好地理解，让我们看看下面的例子。请仔细阅读评论。不要只关注代码逻辑，而是关注函数的结果来发现差异。

**枚举**

它用于创建**用户定义的**数据类型，用于给一个常数指定一个名称，这使得契约更具可读性、可维护性，并且不容易出错。`enums`的选项可以用从 0 开始的无符号整数值表示。它们可以在所有整数类型之间显式转换，但不允许隐式转换。`enums`要求至少有一个成员，其默认值在声明时是第一个成员。

接下来，我们将深入研究**算子**的可靠性。感谢阅读。

**参考文献**

 [## 类型-坚固性 0.7.5 文件

### Solidity 是一种静态类型语言，这意味着每个变量(状态和局部)的类型都需要…

docs.soliditylang.org](https://docs.soliditylang.org/en/v0.7.5/types.html#reference-types) 

# 所有部件正常

*   [合同的布局](https://ferdikurt.medium.com/solidity-fundamentals-88ceb4873064)
*   [值类型:第一部分](https://ferdikurt.medium.com/solidity-fundamentals-ca824a3ac023)
*   [值类型:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-c94460e3be3d)
*   [操作员](/coinmonks/solidity-fundamentals-1fb0e6b3b607)
*   [参考类型](/coinmonks/solidity-fundamentals-e4e4660e16c8)
*   [数据位置和赋值行为](/coinmonks/solidity-fundamentals-a71bf54c0b98)
*   [控制结构](/coinmonks/solidity-fundamentals-1dc2af4b453b)
*   [错误处理:断言、要求、回复和异常](/coinmonks/solidity-fundamentals-a95bb6c8ba2a)
*   [功能:第一部分](/coinmonks/solidity-fundamentals-d2216be1c2c3)
*   [功能:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-functions-a7e3d38c1fe5)

随便问任何问题。

注意安全，做好工作，保持联系！

[费尔迪科特](https://www.linkedin.com/in/ferdi-kurt-8b910b164/)