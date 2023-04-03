# IPFS 的团结

> 原文：<https://medium.com/coinmonks/unity-on-ipfs-e0baa792f014?source=collection_archive---------0----------------------->

## 一款分散式游戏，使用了 Unity、IPFS、Github、Fleek、Unstoppable Domains 和 Pinata。

![](img/06d23fd30e488b7e0d7cd5ac5efcfee2.png)

# 概观

这篇文章将回顾:

*   Unity:安装 Unity 来为 IPFS 创建和导出你的游戏。
*   Github:将你的游戏上传到 Github 上进行持续部署。这将建立 Fleek。
*   Fleek: Fleek 从 Github 获取内容，并在 IPFS 上发布。Github 的任何更新都会自动重新发布。
*   不可阻挡的域名:为你的 IPFS 游戏购买一个人类可读的域名，并与世界共享。
*   Pinata.cloud:钉住图片等静态内容。对 meta 标签有用。

# Unity 设置

访问 [Unity](https://unity.com) 并安装。

![](img/07fe9f286a8d73d014b616d301f155b4.png)

打开 Unity Hub 并创建一个新项目。

![](img/bc86442361c544e8ec238a9c4c059024.png)

在这个例子中，我们将使用一个预建的 FPS 游戏。

![](img/3f457f3799b959db15ca2c01d69faa72.png)

预构建的版本已经可以使用了，所以我们可以开始导出了。

文件>构建设置

![](img/9f01ee16fb5ecee7103d5a0bdecf8127.png)

选择 WebGL 并切换平台。

![](img/3afba47c0d8da39ded51145295cfda21.png)

构建并保存您的项目。

![](img/30e493bf7914d37e0d22ce8e16f82ec6.png)

这些文件将存放在 IPFS。

![](img/42a6f75a32a1e00502b4a88115397c11.png)

# Github 设置

创建一个 Github 账户，进入你的仓库，点击新建。

![](img/80ceda8fa1a0dfe19d2eed73f356ce0d.png)

命名存储库。如果需要，初始化自述文件

![](img/36d63f5aa90965f77f4fcb622d5da826.png)

将从 Unity 导出的文件添加到 Github 存储库中。

![](img/f6f35169e00027b3e0d2c22e80821f32.png)

如果您不熟悉 git，只需将文件拖放到。

![](img/27f2750a42db5b41002da66e013eb304.png)

等等…

![](img/c44cce06d484d52de012d5b1221bf502.png)

编写消息并提交更改。

![](img/e792d3e0aea32b40c39431323d444c6c.png)

最终结果应该是这样的

![](img/2c17eca6bbe7713ba23918abf9e4270d.png)

# Fleek 设置

访问[fleek.co](https://fleek.co/)并用您的 Github 帐户登录。

![](img/cfdb6eac4c27139f7a0c6e7d24b888b5.png)

添加新站点。

![](img/33e4ef584c84805584021cfc310d1494.png)

用 Github 连接。

![](img/a92c4b8ec297b3a23e2fa728452c356b.png)

选择您的 Github 帐户。

![](img/08bb16db4ab7b580e5e51dc6f5fbc8d2.png)

允许对您的项目的权限。

![](img/9006806a99ac4db4af3e1fdb76a2008c.png)

连接后，选择项目。

![](img/5ae925ff99eb31da16f1757a8d29a481.png)

没有多余的配置。部署网站

![](img/1e60e968cdf8c227650bee83dd140962.png)

应该有你的

*   内容标识符(CID): `Qm...`
*   网址:`[https://foo-bar-9999.on.fleek.co](https://foo-bar-9999.on.fleek.co)`

![](img/b8d97f3a2b63b7a2a2bfaea872fab949.png)

试验

![](img/a021eb04f879d544cbdf0a509ab29bca.png)

# 无法停止的域设置

访问[不可阻挡的域名](https://unstoppabledomains.com/)并搜索一个令人敬畏的名字。

![](img/41c1fc6924df38363b85070550d9dd2f.png)

添加到卡中并遵循流程。

![](img/5523d957a7ded05c84f78c9b23159084.png)

一旦购买，访问我的域名和管理。

![](img/3838c6d265783092b22d6c6e1cb28908.png)

点击网站

![](img/7cdbc6296a389b059aef85487e45876c.png)

添加来自 Fleek 的 CID 并保存更改

![](img/1f43f86e0aa1608dee83dde10df43e35.png)

当事务等待批准时，添加不可停止的扩展。这让你可以参观。密码和。zil 域。

![](img/ce8d1b1fcd33309d25ccf2498b0567d5.png)

如果你已经购买了一个. zil 域名，并且不想安装插件，在你的域名后面加上`.sh`。例如，如果您的网站是 helloworld.zil，则使用 helloworld.zil.sh 了解更多信息，请访问 [https://zil.sh](https://zil.sh)

![](img/ff0fabe7735717141a52f36e6ad18a0f.png)

一旦完成，访问并分享你的分散游戏！

![](img/bdb749c066260940bbef95cafd4606be.png)

# Pinata 奖金！

[Pinata](https://pinata.cloud/) 提供了一个易于使用的固定服务，非常适合静态内容。上传图像:

![](img/0fa2a1e3a4096e6ef115884da485053d.png)

然后在元标签中使用它。

```
<meta property="og:image" content="[https://gateway.pinata.cloud/ipfs/QmfTfFRZTac5onepjgqBFDfmnqKcG4dVEBgrNKGqpxLgaD](https://gateway.pinata.cloud/ipfs/QmfTfFRZTac5onepjgqBFDfmnqKcG4dVEBgrNKGqpxLgaD)" />
```

# 结论

本指南是构建更复杂的 web3 游戏的起点。我们希望你能接受这个想法并付诸实践(多人游戏、聊天、加密支付)。我们很高兴看到未来的迭代！

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)