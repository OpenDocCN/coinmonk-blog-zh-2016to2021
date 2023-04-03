# Solidity v0.7.0 来了…

> 原文：<https://medium.com/coinmonks/solidity-v0-7-0-is-here-d585cc83820e?source=collection_archive---------0----------------------->

![](img/655b3b488fe7e124f820c04147bf4055.png)

Intellectual growth should commence at birth and cease only at death. — Albert Einstein

2020 年 7 月 28 日，[以太坊/solidity](https://github.com/ethereum/solidity/releases/tag/v0.7.0) 的开发人员发布了 solidity 编译器的最新版本，v0.7.0。与每个新版本一样，语义和语法都有变化，删除了未使用或不安全的功能，界面也有变化，最后但同样重要的是还有**突破性的变化**。

在这篇博文中，我将试着列出 solidity 0.7.0 带来的变化以及它们的例子。

## 语义变化

非文字的取幂和文字移位将总是使用 uint256 或 int256 来执行操作。以前，在移位量/指数类型中执行操作，这可能会产生误导。

例:
1<x<

以前移位操作选择类型 1(移位量)，现在它将选择类型 x

2 ** x

以前，取幂运算选择 x(指数)的类型，现在它将选择 2 的类型

## 语法变化

*   外部函数和契约创建调用中的 Ether 和 gas 由新语法指定:
    *旧语法:* `contract.function.gas(1000)。value(1 ether)(arg1，…argN)；
    *新语法:*` contract . function { gas:1000，value: 1 ether}(arg1，…argN)；
*   全局变量 **now** 已被弃用，必须使用 **block.timestamp** 代替 now。
    早先:
    修饰符 can retract(uint 256 _ timestamp){
    require(now≥_ timestamp，“不允许提现”)；
    _；
    }
    现在:
    修饰符 can retract(uint 256 _ timestamp){
    require(block . timestamp≥_ timestamp，“不允许提现”)；
    _；
    }
*   NatSpec 对变量的注释只允许用于公共状态变量，不允许用于局部或内部变量。
*   gwei 现在是一个关键字，不能用作标识符。

```
// Earlier
contract Test {
  uint gwei public; 
}// Now
contract Test {
  uint gwei public; // THIS WILL CAUSE AN ERROR
}
```

*   字符串文字将只包含可打印的 ASCII 字符。
*   现在支持 Unicode 字符串文字。它们用 unicode 前缀来标识。

```
// Earlier
contract Test {
    string message public = "Hello 😃"; // message will contain   Hello and not the emoji
}// Now
contract Test {
    string message public = unicode"Hello 😃";
}
```

*   函数的状态可变性现在可以在继承期间受到限制。
    具有默认状态可变性的函数可以被**纯**和**视图**函数覆盖，而**视图**函数可以被**纯**函数覆盖。公共状态变量被认为是**视图**甚至**纯**如果它们是常量的话。

```
Example:
contract Base {
  string public message;
  uint256 public constant value = 10;
  function getMessage() public view returns (string memory _msg) {
    return message;
  } // Default state mutable function
  function test() {
    // do something
  }
}contract Derived is Base {
  function test () public view/pure returns (return something) {
    // return something
  }
  OR
  function test () public pure {
    // do something
  } function getMessage() public pure returns (string memory _msg) {
    return message;
  }
}There are 2 public variables **message** and **value**. In our case **message** will be considered as a **view** function whereas **value** will be considered as a **pure** function.
```

## 内嵌汇编

*   现在不允许在用户定义的函数和变量名中使用`.`。
*   要访问存储变量 x 的 slot 和 offset，现在需要使用 x.slot 和 x.offset，而不是 x_slot 和 x_offset。

## 功能和事件

*   构造函数不再需要可见性。可以通过使用关键字 **abstract 来防止创建合同。**

```
// Earlier
contract Test {
  constructor() public/external {
   // initialisation code
  }
}// Now
contract Test {
  constructor() {
    // initialisation code
  }
}// Preventing a contract from getting created
abstract contract Test {
  // something
}
```

*   不允许对库函数使用虚拟。
*   不允许在同一继承层次结构中有多个具有相同名称和参数类型的事件。

```
// Earlier
contract A {
  event Log(uint256 timestamp);
}contract B is A {
  event Log(uint256 timestamp);
}// Now
The usage of event in contract B is not allowed.
```

*   使用`A for B`只会影响它所提到的契约，更早的时候效果被继承。

```
// Earlier
import 'SafeMath.sol'; // OpenZeppelin SafeMath implementation
contract A {
  using SafeMath for uint256;
}contract B is A {
  // The above SafeMath library will also be used for uint256 in contract B
}// Now
At present using SafeMath for uint256; must be specified explicitly in contract B
```

## 表达式和声明

*   不允许按有符号类型移位。
*   `finney`和`szabo`的面额现在都被去掉了。
*   关键字`var`不能再用了。

## 移除:未使用/不安全的功能

*   包含映射的结构或数组只能在存储中使用。
*   对包含映射的存储中的结构或数组的赋值将不起作用。

## 将代码更新到 0.7.0

*   将`x.f.value(10).gas(30000)`改为`x.f{value: 10, gas: 30000}`。将`(new C).value(10)()`改为`new C{value: 10}()`。
*   将`now`改为`block.timestamp`。
*   将移位运算中右操作数的类型更改为无符号类型。将`A >> (256 — B)`改为`A >> uint256(256 — B)`。
*   `using A for B`如果需要，应在所有衍生合同中使用声明。
*   应从`constructor`中移除可见性说明符。将`constructor() public`改为`constructor()`。
*   如果不创建合同，则从构造函数中删除`internal`关键字，并在合同中添加`abstract`关键字。
*   内嵌汇编中的`_slot`和`_offset`后缀应分别改为`.slot`和`.offset`。

您可以在文档部分找到大部分内容，但是，我会尽可能地添加示例。非常欢迎反馈和建议。深入了解 solidity 以及每个版本带来的变化将有助于您更好地进行智能合约开发。

快乐学习！！！

## 参考资料:

*   [https://solidity .以太坊. org/2020/07/28/solidity-v 0 . 7 . 0-release-announcement/](https://solidity.ethereum.org/2020/07/28/solidity-v0.7.0-release-announcement/)
*   [https://github.com/ethereum/solidity/releases/tag/v0.7.0](https://github.com/ethereum/solidity/releases/tag/v0.7.0)
*   [https://solidity . readthedocs . io/en/latest/070-breaking-changes . html](https://solidity.readthedocs.io/en/latest/070-breaking-changes.html)
*   图片来源:[https://pix abay . com/photos/coding-programming-working-macbook-924920/](https://pixabay.com/photos/coding-programming-working-macbook-924920/)

## 另外，阅读

*   最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [加密复制交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   最佳加密制图工具
*   [莱杰 vs 特雷佐](/coinmonks/ledger-vs-trezor-best-hardware-wallet-to-secure-cryptocurrency-22c7a3fd391e)
*   了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)
*   [3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92)
*   [AAX 交易所评论](/coinmonks/aax-exchange-review-2021-67c5ea09330c) |推荐代码、交易费用、利弊
*   [Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   [FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   [n 零审核](/coinmonks/ngrave-zero-review-c465cf8307fc)
*   [Bybit 交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)
*   [3Commas vs Cryptohopper](/coinmonks/cryptohopper-vs-3commas-vs-shrimpy-a2c16095b8fe)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   最佳 [monero 钱包](https://blog.coincodecap.com/best-monero-wallets)
*   [莱杰 nano s vs x](https://blog.coincodecap.com/ledger-nano-s-vs-x)
*   [bits gap vs 3 commas vs quad ency](https://blog.coincodecap.com/bitsgap-3commas-quadency)
*   [莱杰 Nano S vs 特雷佐 one vs 特雷佐 T vs 莱杰 Nano X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   [bits gap review](/coinmonks/bitsgap-review-a-crypto-trading-bot-that-makes-easy-money-a5d88a336df2)——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   [埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)
*   [赛克斯石评论](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)
*   [BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |从您的密码中赚取高达 8.6%的利息