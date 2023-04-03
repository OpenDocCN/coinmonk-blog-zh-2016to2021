# 减少 EOS 合同部署 RAM 占用空间

> 原文：<https://medium.com/coinmonks/reducing-eos-contract-deployment-ram-footprint-71463f45c43e?source=collection_archive---------2----------------------->

![](img/4c64df85637fda627fa759650a9c18d6.png)

由于 RAM 价格的原因，在 EOS 上做智能合约工作目前有点贵。合同的状态以及代码和 ABI 都存储在基于内存的数据库中。你必须购买的内存，就像我写这篇文章时的价格，大约是 0.7 美元/千字节。

即使你写了一个合同，由用户支付所有数据存储的费用，你仍然需要支付合同的部署费用。

假设你有一个基本契约，你用 [eosio.cdt](https://github.com/EOSIO/eosio.cdt) 来构建，如下所示:

```
eosio-cpp -o player/player.wasm player/player.cpp
```

在本例中，这会产生一个大约 42k 字节的. wasm 文件。因此，要部署它，您可能需要支付 42 * $ 0.7 = $ 29.4

***嗯，原来，EOS 软件为了容纳 VM 等开销，它需要分配十倍的资源。因此，您应该预计需要大约 420 千字节的内存来部署 42 千字节的合同！你现在需要大约 300 美元来部署它。***

因此，当大小的乘数为 10 时，一些大小优化可能会派上用场。我们可以通过在命令行中指定一些额外的参数来实现这一点:

```
eosio-cpp -Os -finline-hint-functions -fno-elide-constructor -o player/player.wasm player/player.cpp
```

通过这种方式，我们指示编译器针对可执行文件的大小进行优化，仅当程序员明确暗示时才内联函数，并禁用特定的复制构造函数优化。

*产生的 wasm 文件现在是 37k 字节，因此成本将是 259 美元。节省下来的 41 美元可以花在咖啡上来促进你的发展。*

我们在这里看到的大约 15%的收益当然会有所不同。你写的代码类型，你使用的库的数量，等等，都可能使这种情况大大增加或减少。我们还应该注意到，这些类型的优化将导致更慢的契约，但除非你做一些计算非常密集的事情，这将不会被注意到。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)