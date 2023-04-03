# 运行“无头”Ganache

> 原文：<https://medium.com/coinmonks/running-a-headless-ganache-b423324ff69?source=collection_archive---------5----------------------->

最近， [truffle](http://truffleframework.com/) 框架转而使用自己的 testRPC 版本，名为“Ganache”。Ganache 有一个很棒的 UI 的胖客户端在这里找到了。这篇文章的目的是向你介绍它的无头版本发现[这里](https://github.com/trufflesuite/ganache-cli)。我们需要一个 ganache 的无头版本，可以在我们的 CI 服务器和预提交钩子上运行测试后安装和拆除。

> 注意，我们安装了 **ganache-cli** 作为开发依赖项，并且在 package.json 中有一个名为 **test 的脚本。**

您应该能够使用 [husky](https://github.com/typicode/husky) 并将“预提交”脚本添加到 package.json 脚本中，以将该脚本连接到您的提交流中。

我希望这对您有所帮助…