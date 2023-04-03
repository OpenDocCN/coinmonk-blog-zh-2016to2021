# 在 electrumX 上开发

> 原文：<https://medium.com/coinmonks/developing-on-electrumx-d1342ab3c8b5?source=collection_archive---------0----------------------->

您可以在很多地方找到关于将 [electrumX](https://github.com/kyuupichan/electrumx) 服务器设置为服务并连接到您选择的区块链守护进程的说明。

[](https://github.com/kyuupichan/electrumx/blob/master/docs/HOWTO.rst) [## kyupichan/electr umx

### electrumx-SPEs Milo/electrum-server 的替代实现

github.com](https://github.com/kyuupichan/electrumx/blob/master/docs/HOWTO.rst) [](https://freedomnode.com/blog/69/how-to-install-an-electrum-server-using-full-bitcoin-node-and-electrumx) [## 如何使用完整的比特币节点和 ElectrumX-freedomnode.com 安装 Electrum 服务器

### 吉娜:如果出现有争议的硬叉子和可能的硬币分裂，如果没有适当的…

freedomnode.com](https://freedomnode.com/blog/69/how-to-install-an-electrum-server-using-full-bitcoin-node-and-electrumx) 

不幸的是，如果您是一名希望将您的资产添加到 electrum 的开发人员，您需要一个开发环境——而不是部署说明。我最近致力于将**颁布的** ( *DCR* )资产集成到 electrumX 中，并且需要一种方法来快速开发和查看我的更改，而无需更新服务和系统守护进程。

下面的要点应该可以让你很快建立并运行一个开发环境(*注意:我在这里使用的是区块链*

感谢[https://github.com/kyuupichan](https://github.com/kyuupichan)和[https://github.com/erasmospunk](https://github.com/erasmospunk)的帮助！

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)