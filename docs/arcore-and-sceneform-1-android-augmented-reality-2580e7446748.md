# ARCore，Sceneform &增强图像#1 Android 增强现实

> 原文：<https://medium.com/coinmonks/arcore-and-sceneform-1-android-augmented-reality-2580e7446748?source=collection_archive---------8----------------------->

什么是 AR 和 ARCore？

![](img/5e215effd70aa476d2cbe920b5b9f0f6.png)

在接下来的系列中，我们将学习 ARCore，并使用谷歌的 [Sceneform](https://developers.google.com/ar/develop/java/sceneform/) SDK 和 [Augmented Images](https://developers.google.com/ar/develop/java/augmented-images/) 来开发一个允许用户在现实世界中放置 3D 模型对象的应用程序。我们将开发一个应用程序，它将检测汽车图像，然后在现实世界中放置一个类似的 3D 汽车模型。酷！不是吗？这就是 ARCore 的强大之处——构建身临其境的用户体验。

# 先决条件

*   Android 开发经验。
*   增强现实支持设备或 AR 支持仿真器(参见此处的[支持设备](https://developers.google.com/ar/discover/supported-devices)和[配置仿真器](https://developers.google.com/ar/develop/java/quickstart#prepare-device)

# 点击此处:

*   第 1 部分—AR 和 ARCore 概述
*   [第 2 部分 Sceneform 增强图像及其核心 API 概述](/@neerajmoudgil/arcore-and-sceneform-2-android-augmented-reality-8108451cd484)
*   [第 3 部分—使用 Sceneform 构建 ARCore 应用](/@neerajmoudgil/arcore-sceneform-augmented-images-3-android-ar-app-2c0990f65df2)

# **什么是 AR(增强现实)？**

简而言之，AR 就是将虚拟物体放到现实世界中。它混合了真实世界和计算机生成的模型。

**AR vs VR**

VR 不与现实世界混合。虚拟现实中的环境可以与真实世界相似，但不是真实的。AR 使用真实世界作为创建沉浸式体验的基础环境。

# 阿尔科尔

ARCore 是谷歌创建 AR 应用的平台。使用不同的 API，它使手机能够感知环境，理解世界和与物体互动。

## 它是如何工作的

ARCore 基于以下基本概念:

1.  **运动跟踪**

运动跟踪使用来自相机和传感器(如加速度计和陀螺仪)的输入来找到用户与环境的相对位置和方向。它在视觉上检测现实世界中的特征点，并跟踪它，这意味着如果您在房间中移动后返回到相同的位置，您应该能够看到相同的对象位于初始位置。

2.**环境理解**

ARCore 足够智能，可以通过检测水平或垂直方向上的特征点簇来检测环境中的水平或垂直平面。我们可以用这个平面来放置虚拟物体。

3.**光估计**

ARCore 允许手机估计当前环境的光线条件，我们可以使用这些功能来创建更多身临其境的体验。你也可以调整颜色。

# 下一步是什么？

到目前为止，我们已经了解了 AR、ARCore 及其基本概念。在下一部分，我们将学习**场景形式**和**增强图像**。

[](/@neerajmoudgil/arcore-and-sceneform-2-android-augmented-reality-8108451cd484) [## ARCore，Sceneform 和增强图像#2 Android AR

### 什么是场景形式和增强图像？

medium.com](/@neerajmoudgil/arcore-and-sceneform-2-android-augmented-reality-8108451cd484) 

**如果帖子有帮助，用掌声鼓励我。分享出来，帮助别人学习。**

![](img/1fe26383361acfff51f623aa906e2165.png)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)