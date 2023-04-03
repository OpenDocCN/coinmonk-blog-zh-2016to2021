# 终于，Solidity v0.5.0 来了！

> 原文：<https://medium.com/coinmonks/finally-solidity-v0-5-0-is-here-801f85176517?source=collection_archive---------0----------------------->

11 月 13 日，期待已久的版本 Solidity i.e. 0.5.0 发布了。在此版本中决定进行许多更改。这个版本有一个很棒的变更日志，使得语言更加强大、成熟，有助于安全开发。

![](img/8758da2f1ba92cb0838eb3019f0fca56.png)

Solidity v0.5.0

以太坊基金会一直在尝试更新 solidity 语言，以使开发更少出错，更健壮。此版本不允许一些不推荐的功能，并强调代码和变量的预期功能的清晰指示。我在这里列出了一些更新和一些代码片段。

# 明确声明

*   将`public`添加到每个函数中，将`external`添加到每个尚未指定其可见性的回退函数或接口函数中。

```
**function**() **external** { x = 1; } // Fallback
```

*   明确说明结构、数组或映射类型的所有变量的数据位置(包括函数参数)

```
// Function parameters along with data locations **function** reveal(
        uint[] **memory** _values,
        bool[] **memory** _fake,
        bytes32[] **memory** _secret
    )
```

*   非常有趣的一点是，声明变量，尤其是函数参数为`address payable`，如果你想对它们调用`transfer`。

```
address **payable** **public** beneficiary; // If you want to transfer funds 
uint **public** auctionEndTime;
```

# 驳回

*   不允许声明空结构。
*   不允许生`callcode`、`var`关键字、`sha3`、`suicide`别名、`throw`语句、`years`单位命名(由于闰年的复杂和混乱)和一元`+`。
*   对于具有一个或多个返回值的函数，不允许使用空的 return 语句。
*   不允许`msg.value`出现在(或通过修饰符引入)一个不可支付的函数中
*   不允许固定大小数组的长度为零。

# 允许

*   支持`pop()`存储数组和`bytes`(不是`string`)从数组末尾删除一个元素。

```
pragma solidity ^0.5.0;contract Array{
    uint256[] public arr;

    function insert(uint256 element) public {
        arr.push(element);
    }

    function removeOne() public {
        arr.pop();
    }
}
```

*   在接口中允许`enum`s&s`struct`s。

```
**interface** Token {
    **enum** TokenType { Fungible, NonFungible }
    **struct** Coin { string obverse; string reverse; }
    **function** transfer(address recipient, uint amount) **external**;
}
```

*   允许`mapping`存储指针作为所有内部函数的参数和返回值。

此版本中还修复了一些错误。这个版本有一个很棒的变更集。你可以访问完整的变更日志[这里](https://github.com/ethereum/solidity/blob/v0.5.0/Changelog.md)。(对开发人员是强制性的)

在这个版本之后，随着语言变得更加丰富，可靠性编码和解析将得到增强😬

祝所有开发者智能编码快乐！

感谢阅读。

版本 1: Solidity **v0.5.1** 已经发布，修正了一些错误，并增加了以下语言特性:

*   允许公共和外部库函数的参数和返回变量的映射类型。
*   允许公共函数覆盖外部函数。

点击查看完整的变更日志[。](https://github.com/ethereum/solidity/releases/tag/v0.5.1)

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)