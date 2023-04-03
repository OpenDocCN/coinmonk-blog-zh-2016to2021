# TODO Dapp 带有 Truffle、React、MobX、NextJS 和 Material UI

> 原文：<https://medium.com/coinmonks/day-25-31-1-year-of-blockchain-afd1fdf40d78?source=collection_archive---------0----------------------->

# 第 5 周–区块链 1 年

我真正需要的是一个项目。没有一个好主意，我想我应该使用一个规范的 TODO 应用程序。也许是有史以来最贵的 todo 应用，每个 todo 项目的交易费约为 1.00 美元。

堆栈:

*   松露
*   反应
*   NextJS
*   Mobx
*   加纳切
*   材料界面(下一页)

首先，我只需要一个样板文件开始，所以我把上面的项目放在一起。

跟随、分叉或查看此处的代码:

[](https://github.com/wrannaman/TODO-Dapp) [## wrannaman/TODO-Dapp

### 一个规范的 todo Dapp，包含 mobx、react、nextjs 和 truffle

github.com](https://github.com/wrannaman/TODO-Dapp) 

挑战:

*检测元掩码*或其他网络连接。除了失败的连接之外，我没有找到其他好的方法来确定用户是否安装了元掩码。你只需要看看 web3 是否被注入，或者你自己是否注入，看看你是否能连接，如果不能，显示一个错误信息。这感觉很笨重。

*中间状态*难以确定。当用户向列表中添加一个条目时，您需要处理事务已经提交的显示(如果事务被拒绝，则显示一个错误)。我在应用程序中没有很好地处理这一点，这让一个用户感到困惑。他们还需要做几个步骤，比如接受 metamask 中的事务，然后等待一段未知时间的响应。

*事件*在应用程序中不起作用。我有一个简单的事件，无法读取事件日志，也无法从 metamask 获取事件，您必须在生产就绪应用程序中进行大量的持久化和检查才能解决这个问题。

简而言之，构建 dapp 就像构建一个应用程序，然后构建另一个应用程序(智能契约+测试+接口)，然后编写大量代码来适应事务和事务状态。很繁琐。工装已经很不错了。松露几乎可以为你做任何事情，而 Ganache 除了暂停一会儿之外，效果很好。编写基本的智能合同在可靠性方面是微不足道的，有一些很酷的项目，如 [ethpm](https://www.ethpm.com) 旨在成为智能合同的 npm。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)

接下来可能是一个更复杂，但小的 Dapp。

[![](img/fcb21bdcb09506038aae23783d457292.png)](https://medium.com/coinmonks/dapp/home)

**Click to read more about Dapps**