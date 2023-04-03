# 如何在 Solidity 中使用合同函数调用和支付的低级调用

> 原文：<https://medium.com/coinmonks/how-to-use-low-level-call-for-contract-function-calls-and-payments-in-solidity-dbd4e7146c14?source=collection_archive---------5----------------------->

![](img/cfbc4d4ca3e7c62ad89ad8c0c1eeed15.png)

Solidity 在`address`数据类型上有`call`函数，可用于调用契约上的公共和外部函数。它也可以用来传输以太网地址。

`call`在大多数情况下不推荐用于契约函数调用，因为它绕过了类型检查、函数存在检查和参数打包。最好是导入契约的接口，在上面调用函数。

`call`用于调用合同的`fallback`和`receive`功能。当在函数调用中没有发送数据并且发送了以太网时，调用 Receive。当没有与调用匹配的函数签名时，调用回退函数。

`call`比调用契约实例上的函数消耗更少的气体。因此在某些情况下`call`是气体优化的首选。

坚固度还有 2 个低级功能`delegatecall`和`staticcall`。`staticcall`与`call`完全相同，唯一不同的是它不能修改被调用合同的状态。`delegatecall`下面讨论。

# 如何使用调用方法？

要使用`call`，您需要将编码数据作为参数发送。数据将函数签名和参数一起编码。

```
**function** myFunction(**uint** _x, **address** _addr) **public** **returns**(**uint**, **uint**) {
    *// do something* **return** (a, b);
}*// function signature string should not have any spaces* (**bool** success, **bytes** **memory** result) **=** addr.call(abi.encodeWithSignature("myFunction(uint,address)", 10, msg.sender);
```

`call`函数的返回值是一个布尔和字节数组的元组。boolean 表示调用的成功或失败状态，bytes 数组包含需要解码的被调用契约函数的返回值。

要获得实际数据类型的结果值，您必须使用`abi.decode`

```
(**uint** a, **uint** b) **=** abi.decode(result, (**uint**, **uint**));
```

decode 方法接受从函数调用返回的字节数组和数据类型元组。返回值的顺序与数据类型元组的顺序相同。

`call`方法不抛出任何错误，代码继续执行。你必须亲自检查成功状态，并根据成功或失败完成必要的任务。

如果传递给`call`方法的函数不存在，则调用契约的`fallback`函数。如果合同中没有实现`fallback`函数，则`call`方法将返回成功状态为假。

# 如何指定气体和转移量

可以向`call`方法提供一定量的气体和乙醚。

```
addr.call{value**:** 1 **ether**, gas**:** 1000000}("myFunction(uint,address)");
```

调用合同上的`myFunction`方法，将只供应指定数量的气体。供应给执行`call`的外部功能的气体量必须大于或等于供应给`call`的气体量。

如果没有提到`gas`，则呼叫前剩余的所有气体都被供应给`myFunction`呼叫。最好不要对提供给函数调用的 gas 进行硬编码。

如果您正在向`call`方法发送 ether，`myFunction`应该是一个`payable`函数。否则调用将失败。

如果供应给`call`的气体量少于`myFunction`完全执行所需的量，调用将失败。

# 如何使用通话发送以太网

在伊斯坦布尔硬分叉`send`和`transfer`方法被弃用之后。伊斯坦布尔硬盘增加了用于从区块链读取一个字的`SLOAD`操作码的开销。Consensys 在[这篇](https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/)帖子中对此进行了解释。

如果在合同的函数调用中使用了`transfer`,固定气体 2300 将被提供给`transfer`方法。由于气体成本会发生变化，因此固定的气体成本可能不足以成功转移乙醚，并且整个函数调用可能会失败。

使用`call`传输以太网打开了可重入攻击的可能性，因为供应的气体可以通过在接收合同的接收或回退功能中再次调用它来重新进入该功能。可重入可以通过使用可重入防护来解决。

如何使用`call`和[重入保护](https://docs.openzeppelin.com/contracts/4.x/api/security#ReentrancyGuard)的示例

```
**function** paySomeone(**address** _addr) **public** **payable** nonReentrant {
    (**bool** success, ) **=** _addr.call{value**:** msg.value}("");
    *// do something* }
```

# 通话与委托通话

`delegatecall`用于从合同 A 中调用合同 B 的一个函数，合同 A 的存储、余额和地址提供给该函数。这样做是为了将契约 B 中的函数用作库代码。因为该函数将表现为它是合同 A 本身的函数。查看[这篇](https://solidity-by-example.org/delegatecall/)文章中的代码示例。

`delegatecall`语法与`call`语法完全相同，只是它不接受`value`选项，只接受`gas`。