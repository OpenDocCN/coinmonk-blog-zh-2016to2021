# 如何优化 ETH 智能合约规模—第 1 部分

> 原文：<https://medium.com/coinmonks/how-to-optimize-eth-smart-contract-size-part-1-a393f444a1df?source=collection_archive---------0----------------------->

根据智能合同实施业务项目是一项相当困难的工作。由于有必要以这样一种方式来设计契约，即操作的执行不会占用大量的气体，因此代码看起来简单明了，甚至考虑到了对具有可靠数据结构的操作的所有限制。

大型项目出现的一个主要问题是，合同规模超过了部署的允许规模，不符合区块链限制。在这种情况下，当您尝试部署契约时，可能会收到类似 open zeppelin sdk 中的`deployment failed with error: oversized data`或 truffle 中的`VM Exception while processing transaction: out of gas`这样的错误。

有许多方法来组织项目中的合同代码，以使每个合同适合所需的大小，但在本文中，我们将研究如何使用库将合同的一些内容移动到库中。

## 什么是最大合同规模，如何检查当前合同规模

契约内容的大小上限约为 24577 字节，您必须将契约逻辑放入这个小尺寸中。

在`truffle compile`对您的合同进行编译后——编译的结果将被保存到`build`文件夹中。然后，您可以通过文件夹中的合同名称访问任何合同，并通过如下代码获得`deployedBytecode`的大小:

```
const contractPath = `build/contracts/MyContract.json`;
const obj = ***JSON***.parse(fs.readFileSync());
const size = Buffer.*byteLength*(obj.deployedBytecode, 'utf8') / 2;console.log('contract size is', size);
```

您可以从 [gist](https://gist.github.com/Jonybang/110799f0765b0da5c4809e3f314f7fa7) 中获得用于记录您在`build`文件夹中的所有合同的脚本。现在让我们来看看解决智能合同大小问题的方法之一。

## 将逻辑从智能合约移动到库

让我们举一个我们可以优化的智能合约的简单例子。以及我们如何使用库来改进这些代码。

这里我们有`PolygonData`结构、`polygons`映射、两个简单的获取和设置数据的函数:`addPolygonPoint` `isInsidePolygonById`和一个复杂的内部函数`isInsidePolygon`。最后一个函数，我们可以把它作为公共函数转移到图书馆。但是因为这个函数使用了`PolygonData`结构，所以我们也应该移动这个函数。让我们来看看结果库:

像`PolygonUtils`这样的库可以通过用`storage`修饰符传递参数来使用其他契约的存储。现在，我们可以访问`PolygonData`结构中的任何字段，即使是数组、映射或其他结构——你可以定义结构中的任何数据，并在库中使用它来获取或设置其中的值。合同现在看起来怎么样？

哦，它看起来很简单也很可爱。ETH smart contracts 中的库——这是一个强大的工具，不仅可以优化您的合同，还可以使您的合同代码更好，并遵循软件开发的 DRY and KISS 概念。

但是不要忘记，您必须在部署和测试中将您的库链接到契约，如下所示:

```
const PolygonChecker = artifacts.require('PolygonChecker');
const PolygonUtils = artifacts.require('PolygonUtils');const utils = await PolygonUtils.new();PolygonChecker.link('LandUtils', utils.address);const checker = await PolygonChecker.new();
```

附注[尼克·穆奇](https://twitter.com/mudgen)和我分享了另一种类似的方式[通过“钻石存储”](https://dev.to/mudgen/solidity-libraries-can-t-have-state-variables-oh-yes-they-can-3ke9)将你的契约逻辑移到库中——就是将结构放在契约存储中明确指定的位置。

你可以在推特里问我和关注:[https://twitter.com/jony_bang](https://twitter.com/jony_bang)

也加入我们的[不和谐社区](https://discord.gg/hjyhqVk)进行讨论。