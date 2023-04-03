# 那么我在哪里训练我的深度学习模型呢？

> 原文：<https://medium.com/coinmonks/so-where-do-i-train-my-deep-learning-model-ebb40769e58a?source=collection_archive---------6----------------------->

> 如果我没有 GPU。

![](img/8180298a51095bc880eb7a906d407497.png)

“Telescope viewfinder on top of the Eiffel Tower in the rain.” by [Made By Morro](https://unsplash.com/@madebymorro?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

所以你最终决定进入深度学习游戏，却意识到在你的笔记本电脑上训练你的深度学习模型需要亿万年，因为要么你没有 GPU，所以你必须在 CPU 上训练你的模型，要么你更不幸，只能用 AMD。不管怎样，我知道那种感觉，真的很糟糕。

> AMD，开始你的游戏吧！

如果您正在寻找 GPU 的经济替代方案，那么我已经帮您找到了。现在我不是专家，也不自称知道每一种可能的方式，但我知道几个最流行和最便宜的。所以让我们开始吧。

> 加油云！

1.  [***paper space***](https://www.paperspace.com/)***:***paper space 因其简洁和便宜的价格成为我的最爱之一。它提供两种主要的服务:*计算*和*梯度。Compute* 为您提供了一台您可以完全控制的机器。你可以根据自己的需要定制。这有点像 AWS 的 EC2 实例。您可以从各种操作系统和 GPU 中进行选择。它很棒，也很快。*渐变*为您提供了一个 Jupyter 笔记本，您可以使用它来构建和运行您的模型。因此，如果你正在寻找更多的控制和定制，使用*计算，*如果你正在寻找简单，然后使用*渐变。*此外，Paperspace 运行在自己的云基础设施上，因此比所有其他替代产品都便宜。它最便宜的价格是 GPU 每小时 40 美分起。此外，每月还有 5 美元的存储费用(对于 ***计算*** 机器),您可以获得大约 50gb 的存储空间。
2.  [***Crestle***](https://www.crestle.com/)***:***Crestle 是另一个为深度学习而生的伟大云提供商。它运行在 AWS 基础设施之上，这可能是它比 Paperspace 贵一点的原因。它为您提供运行在 GPU 上的 Jupyter 笔记本。他们的最低价格是 GPU 每小时 0.59 美元。他们的存储费用是 0.014 美元/GB/天。
3.  [***Floydhub***](https://www.floydhub.com/)***:***Floydhub 是另一个伟大的替代品，就像是*“深度学习的 Heroku”*(用他们自己的话说)。他们还为您提供了一台 GPU 机器，您可以使用 Jupyter 笔记本进行开发。它们还为所有流行的深度学习框架(如 Tensorflow、Keras、Pytorch 等)提供了 Docker 图像的维护集合。就费用/使用时间而言，Floydhub 可能是所有这些产品中最昂贵的替代品。它的最低价是 10 小时 12 美元(也就是每小时 1.2 美元)。
4.  [***AWS***](https://aws.amazon.com/)***:***我估计任何一个云服务商的名单如果不提 AWS 都是不完整的。如果你要进入全面生产模式，并希望完全控制你的 GPU 机器，你肯定会使用 AWS。但是，即使您只是想要一个 GPU 实例进行实验，您也可以获得一个 GPU 驱动的 AWS EC2 实例。你可以完全控制你的机器，就是这样。没有什么是为你设置的。你想做什么都可以，只要你付钱。他们最便宜的 GPU 实例是 0.90 美元/小时，根据您的使用情况，您还会产生一些每月存储费用。
5.  [***谷歌合作实验室***](https://colab.research.google.com/notebooks/welcome.ipynb)***:***我懂了。不是每个人都有钱租用一些云 GPU，并在很酷的深度学习模型中处理这些数据。不要烦恼！我掩护你，雷。这是一个没有多少人知道的资源。也许谷歌希望它那样，我不知道。Colaboratory 是一个研究项目，可以让你免费使用 GPU 驱动的 Jupyter 笔记本 ***！！！*** 为什么免费？我不知道。但是，我不会想太多。使用这个是绝对免费的。你可以将数据存储在谷歌硬盘中，然后运行你的 GPU 驱动的笔记本电脑。显然，既然它是免费的，也有一些缺点。首先，要使用来自 Google Drive 的数据，你需要浏览几个教程来了解如何操作。此外，与其他付费的选择相比，培训有点慢。不能真正用于生产。但是对于实验来说，这是一个很好的免费选择。我不知道还有什么免费的 GPU 资源。此外，您每天大约有 12 个小时左右的计算时间，之后笔记本电脑就会超时。

就这样了，伙计们！这些是我所知道的最便宜和最受欢迎的 GPU 资源。所以，做出选择，开始构建你的深度学习模型。

如果你知道任何其他资源，请留下评论。

***如果你喜欢这篇文章，并想了解更多关于深度学习和人工智能的知识，请跟随我在*** [***炼金术***](https://j-yash.github.io) ***的旅程。***

***有什么问题或反馈吗？留下评论。或者联系我*** [***推特***](https://twitter.com/iamJYash) ***。***

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   面向开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [杠杆代币](/coinmonks/leveraged-token-3f5257808b22)终极指南