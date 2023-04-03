# 如何调试和测试一些基于 Elastos 载波的 dapps 实例，以便更好地理解这项技术

> 原文：<https://medium.com/coinmonks/how-to-debug-and-test-some-example-dapps-based-on-the-elastos-carrier-to-understand-better-this-fe881f96f74?source=collection_archive---------3----------------------->

**1。-先决条件**

**2。-入门**

**3。-理解载体的简要文档和载体源代码的函数和对象属性文件**

**4。-在调试和测试之前运行 nodejs 运营商的演示应用和聊天应用**

**5。-使用 Visual Studio 代码和 Chrome 开发工具调试和测试演示应用和聊天应用**

**a)关于 Chrome 开发者工具的简要文档**

**b)调试演示应用和聊天应用**

# **1。-先决条件**

首先，我们需要安装以下软件，以便调试和测试 Elastos 载波的示例 dapps，并更好地理解这项技术:

*   *Visual studio 代码*。这个免费软件将帮助我们编辑、测试、开发和运行源代码。下面是**【我的** [**安装指南**](https://code.visualstudio.com/Docs/setup/setup-overview)
*   ***Git* 。Git 允许我们从存储库中下载和上传源代码。下面是[**【链接】**](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 下载并安装在电脑上。**
*   ***弹力纤维。NET.Carrier.Native.SDK* 。按照这个 **[** [**链接**](https://github.com/elastos/Elastos.NET.Carrier.Native.SDK)**的说明下载安装。也有视频教程，你可以一步一步地学习安装和更好地理解这个 SDK [**【这里】**](https://www.youtube.com/playlist?list=PLS6d1baqGamKMwQPt51I82L5l1h51qrZA) 。****
*   *****Elastos。NET.Carrier.Nodejs.SDK* 。在你安装完最后一个 SDK 之后，这个 **[** [**链接**](https://github.com/elastos/Elastos.NET.Carrier.Nodejs.SDK)**】**将帮助我们下载、调试和测试基于和欣电信的示例 dapps。****
*   *****Chrome 开发者工具*。为了调试和测试源代码，我们可以用 Visual Studio 代码来做，但最好用 Chrome 开发工具来调试和测试。所以我们需要的是安装 Chrome [**【这里】**](https://www.google.com/chrome/) 如果你已经没有的话。****

# ******2。-入门******

****当所有东西都安装好后，我们需要做的就是打开 Visual Studio 代码并创建一个 workplace 文件。这个 workplace 文件将帮助我们从 Elastos 存储库开始。****

# ****3.- **理解载体的简要文档和具有载体源代码的功能和对象属性的文件******

******【**[**这里**](https://github.com/elastos/Elastos/blob/master/DeveloperGuide/topics/carrier/README.md)**】**我们简单介绍一下说明一下载体。****

****接下来，我们能做的就是学习载体的一个重要文件。本自述文档 [**【文件**](https://github.com/elastos/Elastos.NET.Carrier.Nodejs.SDK/edit/master/docs/README.md)**】**是理解和欣思载体的一个非常重要的部分，如果我们想在和欣思上调试/测试/开发 nodejs dapps 载体****

****在最后一个文件中，我们看到包含了载体的对象的功能和属性，例如邀请朋友、添加/删除/列出朋友、如果我们的朋友接受我们就发送消息、向我们的朋友发送流、…****

# ******4。-在调试和测试之前运行 nodejs 运营商的演示应用和聊天应用******

****下面，我们可以观看运行演示应用程序和聊天应用程序的视频，这样我们就可以了解这两个应用程序的功能，并了解它们如何使用 Elastos 载体作为连接桥梁相互连接和发送消息:****

****以下说明与视频中的过程相同，但采用文本格式:****

1.  ****要了解如何运行这两个应用程序，我们首先要做的是用 Visual Studio 代码打开一个终端，或者您可以只打开一个没有 Visual Studio 代码的终端，然后按照自述文件中的说明进行操作。**【**[**本文件**](https://github.com/elastos/Elastos.NET.Carrier.Nodejs.SDK/tree/master/example/demo)**帮助我们从源代码运行演示 app。******

******2.之后，我们可以从另一个终端运行聊天应用程序，使用 Visual Studio 代码或不使用 Visual Studio 代码。我们可以按照 [**【自述文件】**](https://github.com/elastos/Elastos.NET.Carrier.Nodejs.SDK/tree/master/example/chat-app) 上的说明从源代码运行聊天应用程序******

****(可选)当我们运行聊天应用程序时，我们意识到它是一个使用 node 和 nwjs 的本机应用程序，而不是一个终端应用程序作为演示应用程序。如果我们对开发原生应用感兴趣，我们可以前往 [**【本网站】**](http://docs.nwjs.io/en/latest/) 获取更多文档:****

****3.当执行演示应用程序时，我们在演示应用程序中引入以下命令来了解我们的地址:****

> ****命令:$ address****

****4.然后，我们复制最终终端输出的地址，进入聊天应用程序，键入该地址并在视频下方留言添加为好友，以便演示应用程序收到我们的邀请。****

****5.当我们向演示应用程序发送邀请时，我们可以在我们发送的演示应用程序终端 faccept [chat-app ID]中看到以下消息。所以我们接受朋友在终端中输入这个****

> ****命令:$ faccept[聊天应用程序 ID]****

****6.为了从演示应用程序向聊天应用程序发送消息，我们可以键入****

> ****命令:$ msg[聊天应用 ID] HelloChatApp！****

****7.最后，我们可以从聊天应用程序向演示应用程序发送问候，我们将在演示应用程序终端收到响应。****

# ******5。-使用 Visual Studio 代码和 Chrome 开发工具调试和测试演示应用和聊天应用******

## ******a)关于 Chrome 开发者工具的简要文档******

****如果你是第一次听说 Chrome 开发者工具，并且你想了解更多，你可以去这个 [**【网站】**](https://developers.google.com/web/tools/chrome-devtools) 了解更多:****

****我还推荐观看这个视频，它是关于用 Chrome developer tools 调试 node.js 的****

## ****调试演示应用程序和聊天应用程序****

****在这里，您可以观看调试演练视频:****

****有两种方法可以调试演示应用程序。如果您是第一次调试演示应用程序，建议您在终端的演示应用程序文件夹中编写以下说明，就像我们在上一个视频中看到的那样****

> ****节点— inspect-brk demo.js****

****该命令将在 **demo.js** 文件的第一行代码中停止，这样我们可以更好地学习代码。****

****另一种方法是写作****

> ****节点—检查 demo.js****

****另一种方法是在 Chrome developer Tools 上为演示应用程序的文件 **demo.js** 创建的断点处停止。****

****然后，我们打开 Chrome 浏览器，在上面写下视频中看到的句子。****

> ****镀铬:检查****

****然后，我们点击**打开节点**底部的专用开发工具，我们可以开始调试演示应用程序。****

****为了调试聊天应用程序，因为它是一个本机应用程序，而不是像在演示应用程序中那样使用**节点-检查-制动**，我们首先必须使用 **npm i** 和 **npm start** 来执行应用程序。当聊天应用程序运行时，我们在应用程序上单击鼠标的右下角，然后单击**检查**，正如我们在之前的视频中看到的。然后另一个 Chrome 开发者工具窗口打开，我们点击**源**。我们还需要在 chat-app 的文件 **index.js** 中创建断点，以便调试这个应用程序。****

****在视频中，我们可以看到演示应用程序的 javascript 名为 **demo.js** ，聊天应用程序的 javascript 名为 **index.js** ，其中有许多断点，可以帮助我们调试和测试应用程序。****

****好了，感谢您的阅读和观看，如果您分享任何问题，问题，想法或建议，我们将不胜感激。****

****所以，祝编码愉快，调试愉快😀****