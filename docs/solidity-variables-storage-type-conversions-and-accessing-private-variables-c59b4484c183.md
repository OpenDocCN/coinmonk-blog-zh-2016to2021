# 可靠性变量——存储、类型转换和访问私有变量

> 原文：<https://medium.com/coinmonks/solidity-variables-storage-type-conversions-and-accessing-private-variables-c59b4484c183?source=collection_archive---------2----------------------->

![](img/43855b8f84653f51e0b1e57f942d8c65.png)

这篇文章向初学者介绍了 solidity 如何存储变量，进行类型转换，以及如何检查私有变量的值。这是为了获得在 solidity 中字节级事情是如何发生的基本理解。

这里是我将要研究的变量类型— `uint, string, bytes`

# **变量是如何存储在 EVM 的**

在以太坊虚拟机中，有 3 个区域可以存储数据[ [1](https://docs.soliditylang.org/en/v0.8.6/introduction-to-smart-contracts.html#storage-memory-and-the-stack) ] -

*   **存储** —每个契约都有一个称为存储的数据区，它在函数调用和事务之间是持久的。默认情况下，状态变量(在函数外部声明的变量)被存储并永久写入区块链。存储的使用成本很高，应该谨慎使用。
*   **内存** —第二个数据区称为内存，其中一个契约为每个消息调用获取一个新清除的实例。在函数内部声明的变量是内存，当函数调用结束时会消失
*   **堆栈** —所有的计算都在一个称为堆栈的数据区上进行

我们将查看写入区块链的存储变量。所有变量都以十六进制形式存储在存储器中。

*   `0x` —前缀表示消息的其余部分是十六进制的

以太坊根据变量类型使用两种格式

*   **字符串和字节的 Big endian** 格式
    例如，字符串“abcd”就是这样以十六进制格式(32 字节)存储的:
    `0x6162636400000000000000000000000000000000000000000000000000000000`

十六进制值以大端格式存储在左边，后跟零，而以小端格式存储在右边。

*   小端格式用于其他类型(布尔值、数字、地址等……)。
    数字“10”将被存储为
    

Solidity 将变量存储在存储槽中。solidity 中的每个存储槽长 32 字节。【[2](https://docs.soliditylang.org/en/v0.8.6/internals/layout_in_storage.html)】
`1 byte = 8 bits`这意味着 1 个字节可以存储的最大值为(28–1)= 255。
255 的十六进制表示是`ff`。为了存储更高的数字，将需要另一个字节。这意味着 1 个字节可以存储 2 位十六进制数。所以十六进制数的长度是 32*2 = 64 位数长。

## 单位的存储

让我们来看看 uint 是如何存储的。当我们声明一个变量为 uint 时，默认情况下是 uint256。uint 是无符号类型，这意味着它只能接受正值。

uint256 的长度为 32 字节。数字“256”代表位数。uint256 中可存储的最大编号为
`2**256–1 (as numbers start from 0)`

对于 uint8，最大数量是`2**8–1 = 255`。这将只需要 1 个字节来存储。

对于占用少于 32 字节的类型，solidity 会根据它们在契约中的定义，自动将它们与其他更低字节的变量打包在一起。
例如，如果我们将变量定义为

```
contract A {uint8 a; # goes to storage slot 0
uint8 b; # storage slot 0
uint c; # storage slot 1}
```

它们将仅存储在两个存储槽中，a 和 b 将放在同一个槽中。

然而，如果我们把它们定义为

```
contract A {uint8 a; # goes to storage slot 0
uint c; # storage slot 1
uint8 b; # storage slot 2}
```

变量现在将占用 3 个存储槽。因为在这个例子中时隙是按顺序分配的。

对于更复杂的类型，如映射、动态数组或大字符串，存储槽位置是使用预先确定的公式计算的。你可以参考脚注中给出的 Solidity 文档链接。

# 实度类型转换

## 使用 mod 解释 uint 转换

让我们以下列合同为例

```
pragma solidity ^0.8.6;contract A {uint32 a = 100000;
uint16 public b = uint16(a); //b = a % 65536
uint8 public c = uint8(a); //c = a % 256}
```

任何低阶数字都可以通过在左边加零转换成高阶数字。然而，当我们将高阶数转换为低阶数时，实度会环绕较小的数并给出结果。

在上面的示例中，可以通过将 uint32 100000 放在 max(uint16)或 65536 周围来计算 uint16。
`So b = a % 65536 = 34464`
`Similarly, c = a % 256 = 160`

## 字节 32 至字节 16

```
pragma solidity ^0.8.6;contract C {bytes32 public b32 = bytes32(“This is a big string”);
bytes16 public b16 = bytes16(b32);}
```

b32 的值将是`0x5468697320697320612062696720737472696e67000000000000000000000000`

当你把它转换成 16 个字节时，solidity 会从右边去掉 16 个字节(或者 32 个十六进制数字)。

当 bytesX 转换为 bytesY 时，其中 y< x then x is truncated from the right hand side till the length in bytes is equal to y.

The value of b16 will be 【

## Example with all types

Bytes can be declared directly in solidity by prefixing with 0x.


```
bytes8 example = 0x11030330f020D5C5;

//uint conversion — truncated from left when converting to lower //bytes; adds 0 to the left when coverting to higher bytesuint64 public v1 = uint64(example); 
//uint64 conversion is allowed for bytes8uint32 public v2 = uint32(v1); 
//v2 == uint32(0xf020D5C5)uint96 public v3 = uint96(v1);
bytes12 public b = bytes12(v3); 
//bytes16(uint32(uint64(example))) = 0x0000000011030330f020d5c5bytes4 public b1 = bytes4(v2); 
//bytes4(uint32(uint64(example))) = 0xf020D5C5 //bytes to bytes conversion — truncates from rightbytes4 public b2 = bytes4(example); 
//bytes4(example) 0x11030330 //string conversion — truncates from right when converting to lower //bytes; adds 0 to the right when coverting to higher bytesstring public s = “abcd”;bytes public b3 = bytes(s); 
//b3 = 0x61626364bytes2 public b4 = bytes2(b3); 
//b4 = bytes2(bytes(s)) = 0x6162bytes16 public b5 = bytes16(b3); 
//b5 = bytes16(bytes(s)) = 0x61626364000000000000000000000000string public s1 = string(b3); 
//s1 = “abcd” //address conversionaddress a1 = 0x2014a9707099DFcbA3Bb91D23b31cF7Be61941d9;
bytes20 public b6 = bytes20(a1); // works
bytes32 public b7 = bytes32(abi.encode(a1)); //works //bool conversionbytes32 public b8 = bytes32(abi.encode(true)); //works
```

# 如何访问私有变量

变量的可见性可以是`public, private or internal`。默认情况下，变量是内部变量。`Public`变量可以从任何地方访问。`internal`变量可以被同一个可靠性契约的函数或继承该契约的函数调用。而`private`意味着该变量只能在同一个契约内访问。

然而，由于私有变量的值存储在区块链的 EVM 中，任何人都可以看到它。私有变量的值可以从 EVM 中提取。

例如，让我们以下面的合同为例—

```
 pragma solidity ^0.8.6;
contract A {
uint private secretValue;constructor(uint _value) {
 secretValue = _value; }}
```

我们可以在部署契约时定义 secretValue。无法通过另一个程序查询此变量。但是，它的值将存储在约定的存储槽 0 中。如果我们使用一个简单的`web3js`函数，我们将获得字节码中私有变量的值。【[3](https://web3js.readthedocs.io/en/v1.4.0/web3-eth.html#getstorageat)
`web3.eth.getStorageAt(contractAddress, 0)`

假设我们将 secretValue 定义为 10。然后我会看到这个变量的值为
`0x000000000000000000000000000000000000000000000000000000000000000a`

我们可以很容易地将其转换回十进制值 10。

## 参考

*   [1][智能合约简介— Solidity 0.8.6 文档]([https://docs . Solidity lang . org/en/v 0 . 8 . 6/Introduction-to-Smart-Contracts . html # storage-memory-and-the-stack](https://docs.soliditylang.org/en/v0.8.6/introduction-to-smart-contracts.html#storage-memory-and-the-stack))
*   [2][存储中状态变量的布局— Solidity 0.8.6 文档]([https://docs . soliditylang . org/en/v 0 . 8 . 6/internals/Layout _ in _ Storage . html](https://docs.soliditylang.org/en/v0.8.6/internals/layout_in_storage.html))
*   [3] [web3.eth — web3.js 1.0.0 文档]([https://web 3 js . readthe docs . io/en/v 1 . 4 . 0/web 3-eth . html # get storage at](https://web3js.readthedocs.io/en/v1.4.0/web3-eth.html#getstorageat))