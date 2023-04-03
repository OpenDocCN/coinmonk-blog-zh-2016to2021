# 沃森视觉识别—定制模型

> 原文：<https://medium.com/coinmonks/watson-visual-recognition-custom-models-9cbcc20a6f98?source=collection_archive---------14----------------------->

![](img/1d586b68b48d6b5c66e1fe86b4d7b70b.png)

在图像和视频中识别对象和模式曾经是一项复杂的任务，只有熟悉机器学习的数据科学家和软件工程师才能完成。由于开源库的进步和大量数据的可用性，该领域的准入门槛在过去几年中大幅降低。正如我们将在这篇文章中看到的那样，IBM 通过其 Watson 视觉识别服务使检测图像中的物体变得非常简单。

如果你想跟随而不是阅读这篇博客，这里有[快速视频](https://youtu.be/7gVVJnNZw5o)。

他们提供了几个内置模型

1.  **通用模型** —从数千个类别中默认分类

2.**人脸模型**——年龄和性别的面部分析

3.**显式模型** —图像是否不适合一般用途

4.**食品模型** —专门用于食品的图像

5.**文本模型** —从自然场景图像中提取文本

此外，它们提供了一个非常直观的界面来构建和训练定制模型。你只需要几样东西就可以开始了。

*   IBM Cloud 帐户登录到 Watson 数据平台
*   至少两个类别用于分类，每个类别至少 10 个阳性图像。
*   建议使用不属于任何其他目标类别的阴性样本。

## 训练自定义模型的逐步说明

Watson 视觉识别服务有一个[ [API Explorer 站点](https://watson-api-explorer.ng.bluemix.net/apis/visual-recognition-v3)](https://Watson-API-Explorer . ng . bluemix . net/APIs/Visual-Recognition-v3)，可以用来分析图像。我给了它一个[[Raspberry Pi image](https://www.raspberrypi.org/app/uploads/2017/05/Raspberry-Pi-Model-B-1-462x322.jpg)](https://www . Raspberry Pi . org/app/uploads/2017/05/Raspberry-Pi-Model-B-1-462 x322 . jpg)它返回了以下关键词。

![](img/1375ad49b44faab48b6601846cf5ef1f.png)

成绩一点都不差。该图片被识别为具有高可信度的电路。

> **电气装置— 0.905**
> 
> **电脑电路— 0.904**

这些类别本身是正确的， ***但是我们希望能够区分不同类型的 Raspberry Pi 板。***

## 关于创建自定义模型的分步说明，该模型可以区分不同的 Raspberry Pi 板。

**第一步——登录沃森数据平台**

![](img/932aabf6c0fd7396552522e1537baa03.png)

登录后，您将会在主控制面板上看到有用的提示。

![](img/6dbc35eaa175a0e1650c4d00de13dcc4.png)

**步骤 2——创建一个新的*视觉识别*项目**

![](img/dca3b35bfbcda2947cfa6ff8c34b88b3.png)![](img/e404b2d2963b424fda28df96dfe15357.png)

**第三步——显示定制模型屏幕**

![](img/eb37626f34aa95864646c18c0520dfa6.png)

**步骤 4——将训练图像组织到正确的文件夹中**

我下载了一打 Raspberry Pi 型号 A Plus 和 Raspberry Pi 型号 B Plus 板的图像。此外，我下载了一些消极的形象(董事会都不是)。文件夹结构应该如下所示:

![](img/2130c2e99b9f5173707ed7be5dca0700.png)

**第 5 步——创建要上传的 zip 文件**

![](img/31abd81db64bbfca8df6f6493120ab8b.png)

为所有文件夹完成此操作后，它应该看起来像这样:

![](img/27db0d1925f2f1f081a7bc11355b5080.png)

**步骤 6 —将 zip 文件拖放到资产部分**

![](img/aa468a9c1124e9748a9f7d68a17bfb91.png)

**第 7 步—创建两个类**

*   RaspberryModelAPlus
*   RaspberryModelBPlus

**步骤 8 —将培训文件拖放到各自的文件夹中**

![](img/5870acbe96fc65fa57bab461a52f7668.png)

**第 9 步——就这样！现在让我们来训练这个定制模型**

![](img/ea3f938d08e5d7bd9dc111717a2b044c.png)

**步骤 10——是时候测试我们的模型了**

模型训练完成后，页面会给你一个测试模型的链接。该链接的第一页总结了该模型。

![](img/6fb38e9c915abba0bb84923107a8b113.png)

单击测试选项卡。

我下载了一些不同角度和背景的测试图片，看看模型的表现如何。

![](img/9ad73e03c83db1901c76eb1cdebe9701.png)

将图像拖放到测试页面上。

**测试结果**

***1。树莓 Pi 模型 A Plus 在一个开放的外壳。***

![](img/014c512a6b759d05e5e3749504b9ed06.png)

0.89 还不算太差，因为我们没有在开放的封闭空间内拍摄任何训练照片。

**2*2。树莓 Pi 型号 A 加侧角。***

![](img/347e6d3289a6e5a89497b2cc22bfd76e.png)

我们没有任何这个角度的训练图片。这解释了低置信区间。

***3。Raspberry Pi 型号 A Plus 在封闭电路板中，引脚不可见。***

![](img/ff2dbb17785334d9f3fd9a811b9c941d.png)

这张图很模糊。我认为即使是人类也会有非常低的自信。在这种情况下，模型识别出这是完全错误的板。

***4。树莓 Pi 模型 b+在某人的手中。***

![](img/291f5c493b0e5f8808e6bdcf56f40d92.png)

我希望通过引入一个人来欺骗这张图片的模型，并使实际的电路板变小。但是该模型在这里表现相对较好，置信度为 0.65。这肯定可以通过训练人们手中拿着圆周率的图片来提高。

***5。Raspberry Pi Model B Plus 的颜色在训练图像中不存在。***

![](img/297a028e553e10dadb516b0e7c9db4ef.png)

两个模型的置信区间都很低。我想这是因为我们所有的训练图像都是绿色的。该模型创建层或信息来自学，其中一个重要的信息是颜色。因此，即使测试图像具有正确的结构，模型之前也从未见过红色圆周率。这也可以通过在训练集中包含红色图像来纠正。

***6。树莓 Pi 模型 b+在一个盒子的顶部与远程拍摄。***

![](img/60bbd26ac58a10d95815797febd823d1.png)

训练数据都有特写镜头，这个测试图像离相机有点远。这里的教训是，训练集需要有尽可能多的变化。

**第七期*。raspberry Pi B Plus 型号在外壳中，看不到引脚。***

![](img/3793be261d69978111013aeb3f494368.png)

***8。猎兔犬***

![](img/f5a1aedc6e14ef9a50f2df416e0b6773.png)![](img/1862e5e1fba485f3bdd59c8223c9fa8c.png)

最后两张图片来自不同的电路板。那块特定的木板(不是这些图片本身)是模型被训练的负面场景的一部分。因此，它正确地将该板识别为两个测试类中的任何一个。

## 摘要

所以你有它。我们在 Watson 视觉识别器上建立了一个自定义模型，并训练它检测不同版本的 Raspberry Pi 板，而无需编写任何一行代码或了解机器学习的来龙去脉。**你的模型和它被训练的图像一样好。**如果我用我的 iPhone 拍一张照片，并对这个模型进行测试，我预计我对这两个类别的信心都很低。

本系列的下一篇博客将讨论认知服务可用于以编程方式对图像进行分类的各种 API。我们将在 Glitch 上构建一个简单的应用程序，这是我最喜欢的在线应用程序❤️

![](img/fe6cdb005374976cc4a0e5edcf9d0eaa.png)

[Nick Bourdakos](https://medium.com/u/154a0d2814f0?source=post_page-----9cbcc20a6f98--------------------------------) 有另一篇精彩的[文章，它扩展到了关于 Watson 的 CoreML](https://hackernoon.com/simplify-adding-ai-to-your-apps-core-ml-say-hello-to-watson-c4dfebf66802)。