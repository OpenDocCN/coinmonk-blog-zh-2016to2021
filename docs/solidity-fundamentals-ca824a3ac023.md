# 坚固性基础:类型

> 原文：<https://medium.com/coinmonks/solidity-fundamentals-ca824a3ac023?source=collection_archive---------3----------------------->

## 以太坊智能合约开发

## 值类型:第一部分

![](img/9f80214e8f84a8115be619cac3838d54.png)

让我们简单看一下这一部分将包括什么。我们将检查通过值传递的**值类型**，此外，如果值存储它自己的数据而不是指向特定内存位置的指针，它被称为值类型，也被称为原始数据类型。下面是我们将要讨论的值类型；

*   布尔运算
*   整数
*   地址
*   地址文字
*   固定大小的字节数组
*   动态大小的字节数组(字节，字符串)
*   枚举

Solidity 是一种静态类型语言，这意味着每个变量都必须在编译时指定。以这种方式，类似于 **Java** 、 **C** 、 **C++** 、 **Scala** 等。此外，类型可以在包含运算符的表达式中相互交互。接下来我们将在 Solidity 中有一个关于*操作符*的部分，这样事情将一部分一部分地变得清晰。

与 **Javascript** 、 **C#** 或 **Perl** 、 *null* 或 *undefined* 值不存在于 **Solidity** 中，但是默认值取决于类型。例如，如果我们声明了一个 boolean 类型并且没有指定任何值，那么这个变量的默认值将是 false。

**布尔型**

可能的值有*真*和*假*。

我知道在下面的合同中有新的关键字，但请仔细阅读评论，只关注`boolean`值类型。稍后我们将在它们自己的部分解释这些新的关键字。

**整数**

Solidity 可以管理各种大小(最大为 256 位)的有符号/无符号整数*以及开发用定点数(以 un *fixed/fixed* 的形式)，该语言还不完全支持，可以声明，但不能赋值给或从。*

朋友们，请注意，我们将要谈论一个非常可怕的场景，它会造成巨大的伤害。

当`uint`(无符号整数)达到其**字节大小**时，添加的下一个值将返回第一个变量元素。

> **注** : `1 byte = 8 bits`

假设我们有一个`uint8`，也就是`8`位。这意味着我们可以存储的最大数是等于 T3 的 T2。看下面的例子。如果我们执行`increment`功能会发生什么？这叫做**溢出—** 意思是我们新的`num_one`是 0。

**下溢**类似，如果我们从`0`中减去`1`，新的结果将是`255`。我们将在后面的章节中讨论如何避免这种情况。

**地址**

每个帐户和智能合同都有一个存储为 20 字节的地址。地址用于跨帐户发送和接收以太网。这可以认为是区块链上的公共身份或者账号。

地址类型有两种类型，`address`和`address payable`—`address payable`可以接收以太网，而`address`不能。

`msg.sender`是 Solidity 中的内置函数，表示当前与合同连接的地址。更其类型是`address payable`。

`balance`返回卫中的地址余额。

> **注:** 1 乙醚= 1^18 魏= 10 亿魏

`transfer`将卫中的乙醚量转到一个应付地址。如果平衡。该函数在失败时恢复，停止当前契约，并在任何错误时抛出异常。

> **注**:在后台，`transfer`函数在发送以太网之前，通过应用属性`balance`查询一个地址的余额。

`send`是`transfer`的低级对应。如果执行失败，当前合同不会异常停止，但是`send`会返回`false`。

还有`call`、`delegatecall`、`staticcall`都是地址类型的成员。我们将在后面详细讨论这三个重要功能。

从`address payable`到`address`的隐式转换是允许的，而从`address`到`address payable`的转换必须通过`payable(<address>)`显式进行。

**地址文字**

地址文字是以太坊地址的十六进制表示，包含 40 个字符(20 个字节)，前缀为`0x`。

十六进制文字必须通过地址校验和测试，否则校验和测试会产生错误。

> ***注*** *:* 混合地址校验和格式在 [EIP-55](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md) 中定义

让我们用一些例子来消化上面所有的信息。

接下来，我们将继续并完成**值类型**。感谢阅读。

**参考文献**

 [## 坚固性-坚固性 0.7.5 文件

### Solidity 是一种面向对象的高级语言，用于实现智能合约。智能合同是程序…

docs.soliditylang.org](https://docs.soliditylang.org/en/v0.7.5/index.html) [](https://ethereum.stackexchange.com/questions/3542/how-are-ethereum-addresses-generated) [## 以太坊地址是如何产生的？

### 私钥空间:这里是一些代码示例，基于以太坊使用的椭圆曲线 secp256k1，正如其他人已经…

ethereum.stackexchange.com](https://ethereum.stackexchange.com/questions/3542/how-are-ethereum-addresses-generated) 

# 所有部件正常

*   合同的格式
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