# 元掩码更新—隐私模式

> 原文：<https://medium.com/coinmonks/metamask-update-privacy-mode-5439bb6253bb?source=collection_archive---------4----------------------->

几个月前，Metamask 的 awesome 团队发布了一篇博客文章([https://medium . com/metamask/https-medium-com-meta mask-breaking-change-injecting-web3-7722797916 A8](/metamask/https-medium-com-metamask-breaking-change-injecting-web3-7722797916a8))，声明从 11 月 2 日起，meta mask 将进行更新，以停止 web 3 默认暴露用户帐户；而是要求 dApp 明确请求访问元掩码帐户的许可。

目前，“隐私模式”推出时，仍将被默认禁用；但是，在将来的版本中，它最终会默认启用。

所以让我们来看看这个是什么样子的。

在这里做一个快速测试，用我当前安装的 Metamask 来检索 Metamask 的第一个帐户。

![](img/2e9fd45f43ee316ab846a93c94259c45.png)

Works as is.

![](img/f5c7ad772bf0528a9f038c62ba54d377.png)

Using a pretty standard provider listener.

一切都很好。现在我们将安装自定义元掩码构建发现[这里](https://github.com/MetaMask/metamask-extension/pull/4703#issuecomment-434615101)，并打开隐私模式:

![](img/b17fd33e1c7a0cc899cf72d90e18e897.png)

Turning Privacy Mode on.

![](img/8986bca824f5b9cbf05a4ea3ddaeb89a.png)

And it fails to get the address now!

现在我们看到隐私模式确实阻止我们直接访问元掩码，让我们添加启用提供者所需的代码:

![](img/26a49c1b4be2acb2474e4c042a35fd6e.png)

Creating an async function to ask for permission.

![](img/001e92204f859a7e20eed420fb7dcbfc.png)

Detect web3 provider and calling for permission.

刷新我们的测试，我们应该会看到一个提示:

![](img/3a50f5d720f69cdd0a39e55f8120a068.png)

Prompting for permission — Click Reject/Approve to your fancy.

![](img/b0a99ef29b8c16b407e46f2e08ad9f7b.png)

After we click Approve — Refresh the page and it works!

所选的设置将被 Metamask 记住，并且可以通过点击选项正上方的“清除隐私数据”来清除，以启用“隐私模式”

就是这样！非常简单。非常感谢 Metamask 团队做出了如此必要的改变——哦，期待着在 DevCon4 上发布的 Metamask 移动客户端！；)

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)