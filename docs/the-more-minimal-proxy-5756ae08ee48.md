# 更简单的代理

> 原文：<https://medium.com/coinmonks/the-more-minimal-proxy-5756ae08ee48?source=collection_archive---------0----------------------->

![](img/19299aac6215768af17c627d3fdc1457.png)

如果你曾经研究过如何廉价地大规模部署合同，你可能会遇到 [EIP-1167](https://eips.ethereum.org/EIPS/eip-1167) 。这些最小代理契约对一个固定地址执行一个`DELEGATECALL`，并且它们只用**45 个字节** *(或者更少，如果你代理到一个从零字节开始的紧凑地址】*，这使得**部署起来更加**有效，代价是使用它会增加一些开销。

标准正在激增，而且理由很充分——对于一组具有重复逻辑的合同来说，让该逻辑只存在于一个地方*是一个好主意(只要没有人使用该逻辑并且* [*意外地杀死了它*](https://github.com/paritytech/parity-ethereum/issues/6995#issue-271523889) *，也就是)*。例如，它现在是 OpenZeppelin SDK 的一部分(他们刚刚发布了一个关于 EIP-1167 如何工作的[很棒的初级读本](https://blog.openzeppelin.com/deep-dive-into-the-minimal-proxy-contract/))。见鬼，我自己不久前为擦除协议写了一个库，名为 [Spawner](https://github.com/0age/Spawner) ，它给出了 EIP-1167 代理。重点是，这个 EIP 现在是一位数的 ERC 公司，实际上在野外被采用。

这就是为什么我想在 EIP-1167 达到临界质量之前解决它一直困扰我的问题。也就是说，这个:**它不像它可能的那样最小**。

这里是有问题的字节码，用 EIP 本身的说法拼写出来:

```
0x363d3d373d3d3d363d73bebebebebebebebebebebebebebebebebebebebe5af43d82803e903d91602b57fd5bf3pc    op / pushdata  opcode              stack (top on the left)
----  -------------  ------------------  -----------------------
0x00  36             calldatasize        cds
0x01  3d             returndatasize      0 cds
0x02  3d             returndatasize      0 0 cds
0x03  37             calldatacopy        
0x04  3d             returndatasize      0
0x05  3d             returndatasize      0 0 
0x06  3d             returndatasize      0 0 0
0x07  36             calldatasize        cds 0 0 0
0x08  3d             returndatasize      0 cds 0 0 0
0x09  73bebebebebe.  push20 0xbebebebe   0xbebe 0 cds 0 0 0
0x1e  5a             gas                 gas 0xbebe 0 cds 0 0 0
0x1f  f4             delegatecall        suc 0
0x20  3d             returndatasize      rds suc 0
0x21  82             dup3                0 rds suc 0
0x22  80             dup1                0 0 rds suc 0
0x23  3e             returndatacopy      suc 0
0x24  90             swap1               0 suc
0x25  3d             returndatasize      rds 0 suc
0x26  91             swap2               suc 0 rds
0x27  602b           push1 0x2b          0x2b suc 0 rds
0x29  57             jumpi               0 rds
0x2a  fd             revert
0x2b  5b             jumpdest            0 rds
0x2c  f3             return
```

当你浏览这个的时候，有什么吸引你的呢？我将告诉你我最感兴趣的是什么——`swap`操作码。我们可以做得更好:

```
0x3d3d3d3d363d3d37363d73bebebebebebebebebebebebebebebebebebebebe5af43d3d93803e602a57fd5bf3pc    op / pushdata  opcode              stack (top on the left)
----  -------------  ------------------  ------------------------
0x00  3d             returndatasize      0
0x01  3d             returndatasize      0 0 
0x02  3d             returndatasize      0 0 0
0x03  3d             returndatasize      0 0 0 0
0x04  36             calldatasize        cds 0 0 0 0
0x05  3d             returndatasize      0 cds 0 0 0 0
0x06  3d             returndatasize      0 0 cds 0 0 0 0
0x07  37             calldatacopy        0 0 0 0
0x08  36             calldatasize        cds 0 0 0 0
0x09  3d             returndatasize      0 cds 0 0 0 0
0x0a  73bebebebebe.  push20 0xbebebebe   0xbebe 0 cds 0 0 0 0
0x1f  5a             gas                 gas 0xbebe 0 cds 0 0 0 0
0x20  f4             delegatecall        suc 0 0
0x21  3d             returndatasize      rds suc 0 0
0x22  3d             returndatasize      rds rds suc 0 0
0x23  93             swap4               0 rds suc 0 rds
0x24  80             dup1                0 0 rds suc 0 rds
0x25  3e             returndatacopy      suc 0 rds
0x26  602a           push1 0x2a          0x2a suc 0 rds
0x28  57             jumpi               0 rds
0x29  fd             revert
0x2a  5b             jumpdest            0 rds
0x2b  f3             return
```

看到了吗？少了一辆`swap`，多了一辆`returndatasize`而不是一辆`dup`。这转化为部署*(运行时代码少一个字节)*少 200 个 gas，调用*少 4 个 gas(我们去掉了成本三个 gas 的* `swap` *，* `returndatasize` *比* `dup` *)* 。

这是客观的改进，但是值得花费额外的努力来更新所有的工具，或者甚至修改 EIP 或者起草一个新的吗？我不知道。如果你有这样或那样的强烈意见，请告诉我。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)[![](img/a06b758bdcc47dca7c2504f298674d87.png)](https://coincodecap.com)