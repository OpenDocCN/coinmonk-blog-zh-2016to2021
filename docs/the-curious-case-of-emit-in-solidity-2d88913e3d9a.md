# Solidity 中“emit”的奇怪情况

> 原文：<https://medium.com/coinmonks/the-curious-case-of-emit-in-solidity-2d88913e3d9a?source=collection_archive---------1----------------------->

![](img/7dc5e9f0712949bfa39d7b958cb0cf36.png)

Photo by [Jodie Stallard](https://unsplash.com/photos/IBan90OGoIU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/water-hose?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

我已经开始喜欢在 Solidity 中编码，因为它提供了如此多的创新特性。我在以前的[文章](/coinmonks/the-curious-case-of-in-solidity-16d9eb4440f1)中写过关于“_”修饰语的内容。在这篇文章中，我希望记录下`emit`在 Solidity 中做了什么。

固体中的一个`emit`触发一个事件。把一个事件想象成点击用户界面上的一个按钮——但这不是 Solidity Smart Contract 触发的事件类型。这里有一个…