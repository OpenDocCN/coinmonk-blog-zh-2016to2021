# Qtum Dapp 开发人员 QTUM AMI 在 AWS EC2 实例上的基本用法

> 原文：<https://medium.com/coinmonks/qtum-dapp-developper-basic-usage-of-qtum-ami-on-aws-ec2-c9c234d578ce?source=collection_archive---------5----------------------->

![](img/8bb82e5a15a7d199c02443df6dd5a97b.png)

AWS — QTUM AMI

2018 年 7 月， [AWS](https://aws.amazon.com/) 发布了 [QTUM 亚马逊机器图像](https://aws.amazon.com/marketplace/pp/B07FB214D3) (AMI)。
这个图像使得快速开始使用 [QTUM](https://qtum.org/en) 成为可能，而不必过于担心配置。

## 先决条件:

*   **QTUM AMI 的一个运行实例，可以用 ssh 连接到**。如果您没有和/或不知道从哪里开始，请查看我以前的文章:[在 AWS EC2 实例上运行 QTUM AMI](/@clement0bresson/qtum-set-up-a-node-with-aws-b76b6b56ec71)
    它将帮助您设置、运行和连接这样的实例。

本教程面向愿意使用 QTUM AMI 的 QTUM Dapp 开发人员。

QTUM AMI 附带已经安装的 **Qtum Core** 、 **Qmix web IDE** 和 **Solidity** 。我们将看到如何与他们三个互动。

接下来的所有教程都假设你用 ssh 连接到你的实例(如果需要的话，参见[上一篇文章](/@clement0bresson/qtum-set-up-a-node-with-aws-b76b6b56ec71)):

# 1 —发布 QTUM 核心

首先，QTUM AMI 安装了 **qtumd** 。这是用于启动 QTUM 核心的命令。

从实例上的 QTUM 节点开始:

**qtumd**

如果你想启动 testnet，可以使用 **-testnet** 选项(默认情况下 mainnet 启动)

如果您希望进程在后台运行，那么您可以使用 **-daemon** 选项，这样您就可以在以后运行命令。如果像这样作为守护进程启动它，如果以后需要停止它，可以使用命令: **qtum-cli stop。**

注意:你可以通过命令获得 qtumd 的帮助: **qtumd -h**

**qtumd** 命令将启动 **Qtum 内核**。你会看到一个**。出现在 **/home/ubuntu** 中的 qtum** 目录。这个目录是存储本地链信息的默认目录(在 Linux 中，安装了 OS AWS)。*请注意，如果需要，您可以更改这个位置，如果需要，请查看 qtumd 帮助。*

当您启动 qtumd 时，您的本地链将自动开始同步。

与正在运行的 Qtum 核心交互的基本命令是:

**qtum-cli**

要获得帮助，请使用:

**qtum-cli —帮助**(显示 qtum-cli 的帮助)
**qtum-cli** (显示命令列表)
**qtum-CLI[命令] —帮助**(显示命令的帮助)

您可以使用以下命令验证您的节点是否连接到 QTUM 网络:

**qtum-cli getnetworkinfo**

您应该看到显示:
**“网络活动”:true，“连接”:X** ( > 0)

## 如何知道你的节点已经完成与网络的同步？

我第一次在处理 qtumd 的时候发现很难知道。解决方案其实很简单。运行命令:

**qtum-CLI getblockchaininfo**

并检查值:**验证进度**。如果是 **1** ，那么你的本地链是完全同步的。

*注意:同步可能需要几个小时才能完成，这取决于您为 EC2 实例选择的实例类型、网络、节点…请耐心等待:)*

**qtum-cli** 将是您在本地(直接在实例上)与节点通信的首选命令。有了它 **y** ou 就可以举个例子:

*   获取新的 QTUM 地址(记住，对于 UTXO 模型，一个钱包有许多地址，理想的情况是每笔交易一个地址)
*   发送/接收 QTUM 令牌，
*   发送交易记录
*   签署交易记录
*   标桩 QTUM 标记
*   保护你的钱包
*   …等等。

本教程不会涵盖 qtum-cli 提供的更多可能性。如果你想了解更多，我建议你先看看官方文档: [https://docs.qtum.site](https://docs.qtum.site)

如果你想只从实例中直接使用 **QTUM core** (使用 qtum-cli)，你可以直接进入下一部分。然而…

## …如果您想从外部使用您的新鲜节点，该怎么办？

例如，从您的 Dapp 逻辑所在的客户端和/或服务器？

为此，你需要做更多的设置来使你的 QTUM AMI 可用，我发现很难找到这方面的信息…所以这里是:

## 让我们公开 AWS EC2 3889 和 13889 端口

首先…您必须知道，默认情况下，您的 QTUM AMI 不会暴露超过 5555 个用于 QMIX 的端口(参见下一节)、22 个用于 SSH 连接的端口(我们在上一教程中用来连接 EC2 实例)和 3888 个端口。

*但是*，当你运行 **qtumd** 或者 **qtumd -testnet 时，默认使用的端口是 3889 和 13889。(mainnet 为 3889，testnet 为 13889)**

因此，按照以下步骤在 AWS EC2 上打开这些端口:

1/返回到*服务*>*EC2:*[https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)

2/点击*运行实例*

3/添加 QTUM 实例的行尾，在*安全组列*中，您将看到一些链接:*QTUM AMI-QTUM AMI 1–0-AutogenByAWSMP-2*。*点击上面的*

4/在页面底部，点击*入站*选项卡。

5/点击*编辑*

6/ A 模态打开，点击*添加规则*

7/对于新规则，选择**自定义 TCP 规则**，端口范围 **3889，**源 **0.0.0.0/0。**如果您也想从实例外部调用 testnet network，对端口 **13889** 做同样的事情。当然，如果您在启动时更改了 **qtumd** 的默认端口，就使用它们(尽管如果您按照教程操作，就使用默认端口)。

8/点击*保存*。

9/恭喜，**您的 AWS EC2 实例现在接受到 3889 和 13889 端口的连接**。

## 现在，让我们重新启动 qtumd 来定义授权用户/ ip，以便从外部调用您的节点

1/如果 **qtumd** 正在运行，用 **qtum-cli stop** 停止

2/使用命令再次启动它:

**qtumd-RPC user = clement-RPC password = test-rpcallowip = XX。XXX.XX.XXX/255.255.255.0**

注意: **clement** 应该替换为您想要的**用户名**， **test** 应该替换为您想要的**密码**和 **XX..**通过你的 **IP** (或者客户端调用它的 IP)。稍后将需要您设置的用户名和密码来调用正在运行的 Qtum core。
请注意，如果您需要授权多个 ip，您可以多次使用 **-rpcallowip** 选项。

3/考验的时候到了！从以前授权的 IP，尝试以下命令:

**curl-user element:test-data-binary ' { " JSON RPC ":" 1.0 "，" id":"curltest "，" method": "getwalletinfo "，" params ":[]} '-H ' content-type:text/plain；'**[**http://XXX.XXX.XXX.XXX:3889/**](http://ec2-35-178-153-173.eu-west-2.compute.amazonaws.com:3889/)

*别忘了用你刚刚设置的用户名:密码和* [*XXX 替换 clement:test。XXX.XXX.XXX*](http://ec2-35-178-153-173.eu-west-2.compute.amazonaws.com:3889/) *由* *您的 AWS EC2 实例 IPV4(不是您的授权 IP:)。*

…您应该会得到结果！**恭喜恭喜！**

现在，您有一个 Qtum 节点正在运行，并且可以从外部与它通信，使用您定义的用户/密码和您授权的 IP。

现在，您可以在项目中使用它与 QTUM 区块链进行交流。

例如，使用 Qtum 的 [QtumJS](https://qtumproject.github.io/qtumjs-doc/#qtumrpc) ，您可以:

`const rpc = new QtumRPC('http://clement:test@XXX.XXX.XXX.XXX:3889');
const info = await rpc.rawCall("getwalletinfo");`

# 2—使用 QMIX

QMIX 是以太坊 Remix 项目的 QTUM 等价物。

它允许你在 QTUM 区块链上**编写**、**调试**、**编译**、**部署**智能合约。不错吧？

实际上…它已经在 EC2 实例的端口 5555 上运行并可用。

如果正在运行的 AWS EC2 实例的 Ipv4 地址是:

111.222.333.444

*(注意，您可以在您的 AWS EC2 仪表盘上找到它:*[*https://console.aws.amazon.com/ec2/*](https://console.aws.amazon.com/ec2/)*)*

那么 QMIX 可以在 URL:
http://111 . 222 . 333 . 444**:5555**找到

本教程就不深入 QMIX 了。
然而，如果 QMIX 让你感兴趣(应该是这样)，你可以查看[这篇为 QTUM Dapp 开发者初学者写的文章](https://blog.qtum.org/qtum-dapp-developer-a-quick-start-guide-ae18cea28951):

里面有一个视频，里面有一个非常好看的 QMIX 演示。

# 3/坚固性

在 AMI 中，Solidity 编译器也是可用的。您可以使用以下命令来访问它:

solc ( —如果你想知道如何使用它，请提供帮助)

很多关于 solc 用法的教程都可以在网上找到，所以我也不会在这里讨论。

仅此而已！感谢 AWS 上的 QTUM AMI，您现在应该对如何开始使用 QTUM 有了更好的理解。

**希望你喜欢&快乐编码！**

## 更进一步的一些链接:

*   [QMIX](https://github.com/kfichter/qmix)
*   [Solc 文档](https://solidity.readthedocs.io/en/v0.4.24/installing-solidity.html)
*   [QtumRPC 方法](https://qtumproject.github.io/qtumjs-doc/#qtumrpc)(您可以使用命令: **qtum-cli【方法】**来访问它们)
*   [官方 Qtum 文档](https://docs.qtum.site)
*   特别是 [qtumd / qtum-cli doc](https://docs.qtum.site/en/Guidance-of-Qtum-Deployment-and-RPC-Settings.html) (其中一些是用来写本教程的):

如果你有任何想法/建议，发现了错误，和/或希望我更详细地描述某些部分，请不要犹豫评论这篇文章，这样我就可以改进这篇教程。

*你喜欢你读到的东西吗？帮我投入更多的时间在 QTUM 上写作。
QTUM 地址:****qyc 3 ftqyvztyucrrvvfv 4 qkhk 5 GQ 4 wkie***

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)