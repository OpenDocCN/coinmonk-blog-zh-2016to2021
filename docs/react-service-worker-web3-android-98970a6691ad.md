# React 服务人员+ Web3 + Android = (╯ □ )╯︵ ┻━┻

> 原文：<https://medium.com/coinmonks/react-service-worker-web3-android-98970a6691ad?source=collection_archive---------0----------------------->

## 开发者请注意:如果你正在使用 Create React App 构建你的 DApp，请注意 React 服务人员！

在#ETHDenver 之后的两个月， [Cryptogs](https://cryptogs.io) 已经准备好全面投产。我在我能找到的每一台设备上都测试了它，效果非常好。Metamask、Cipher、Toshi 和 Trust 看起来都不错……是时候伸出手来上市了。

我从 [Toshi](https://medium.com/u/32a7eeb3e4cf?source=post_page-----98970a6691ad--------------------------------) 的 [Sid](https://twitter.com/Sid_Coelho) 那里得到了快速的反馈:我的应用程序没有接收到他手机上注入的 web3。:(

我很快检查了我所有平台上的所有设备，看起来很棒。然后，我从朋友那里掏出一个旧的安卓手机，果然。没有网络 3。WTF！

让我更困惑的是，游戏在舞台上运行得很好。一开始我以为是 CloudFront，SSL，Web3，Android 的问题。但在确保我的舞台环境后，它仍然有效。我不得不深入挖掘…

我想把问题简化到最基本的部分。从生产环境开始:Cloudfront，SSL，webserver，捆绑的 js 等…

我决定创建一个小的开源 repo，并将其托管在: [https://web3.wtf](https://web3.wtf)

果然，这个网站会重现问题；用 iphone 或从桌面访问它，你会看到 web3 连接。如果你从一个旧的 android 手机上点击它，它可能会在第一次加载时工作，但在一两次重新加载后，噗。

经过大量的故障排除，我发现我的后端从来没有收到任何来自旧 Android 手机前端的请求。某些东西导致它在“离线”模式下运行，并且没有检测到注入的 web3。事实上，我可以**关闭托管我的前端**的机器，游戏仍然会出现在 [Toshi](https://medium.com/u/32a7eeb3e4cf?source=post_page-----98970a6691ad--------------------------------) 、 [Cipher](https://www.cipherbrowser.com/) 或 [Trust Wallet](https://trustwalletapp.com/) …但没有 web3。

我爱 CRA，但 React 的服务人员对 DApps 来说是个十足的 Jabronie！

如果您正在使用 Create React App，并且在检测注入的 web3 时遇到问题，请尝试注释掉 index.js 中的 ServiceWorker 内容:

Notice on line 7 and 8 I even go so far as to “unregister()” to make sure to kill the beast.

在我将上面的代码部署到产品中之后，一切都很顺利，现在来自世界各地的人们正在[扔大锤和收集分散的 pog](https://cryptogs.io)！

![](img/cf29634ad71da5b1d1df28a441b5b722.png)