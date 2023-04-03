# 在 DAPP 中执行 web 查询的指南(第 2 部分)

> 原文：<https://medium.com/coinmonks/a-guide-to-perform-web-queries-in-dapp-part-2-98a519399d57?source=collection_archive---------11----------------------->

在理解了第 1 部分中 Oraclize 的[机制之后，我们可以深入实现 DAPP 的细节。](/coinmonks/a-guide-to-perform-web-queries-in-dapp-35683a386044)

我们将执行一项合同，该合同:

*   使用 coindesk api 获取最新的 BTC/美元数据
*   每 60 秒更新一次

注意 Oraclize 官网使用的 fixer.io api 已弃用。并且本例中使用的 coindesk api 将来也可能被弃用。因此，在遵循本教程之前，请验证 api。

# 使用 Oraclize api

该合同应该是合同`**usingOraclize**`的**子合同**

但是，您可能会发现 import 语句不起作用。一个快速的解决方法是从他们的 [**repo**](https://github.com/oraclize/ethereum-api/) 下载`oraclize.sol`文件到你的项目目录。并使用 [**实体导入语句**](https://remix.readthedocs.io/en/latest/tutorial_import.html) 导入`oraclize.sol`:

`import "oraclize.sol";`

# 添加函数进行查询

然后，我们将实现更多的功能:

*   每 60 秒更新一次
*   使用“URL”数据源类型
*   使用 json 解析助手获取. rates.GBP 属性

通过在回调中添加一个`updataPrice`函数。更新过程将再次启动。我们指定`updataPrice`功能在 60 秒后执行。因此，每隔 60 秒，我们就会得到一个新的数据。

# 处理数据

然后我们定义一个处理函数来记录数据。

```
event LogPriceUpdated(string price); //a function that will log the input string
```

# 把所有的放在一起

为了保持简洁明了，我们跳过了一些功能。这是使用 Oraclize 进行 web 查询的最小可行代码片段。

# 整个代码片段

该代码最初来自 http://docs.oraclize.it/#ethereum-quick-start 的。

*原载于*[*gist.github.com*](https://gist.github.com/sksitou/4b79f2726b4cfccf94b2db9d3074ce19)*。*

[在 DAPP 中执行 web 查询的指南(第 1 部分)](/@thebestchef/a-guide-to-perform-web-queries-in-dapp-35683a386044)
[在 DAPP 中执行 web 查询的指南(第 3 部分)](/@thebestchef/a-guide-to-perform-web-queries-in-dapp-part-3-de27ceb5343a)