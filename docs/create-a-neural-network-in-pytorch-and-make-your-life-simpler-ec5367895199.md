# 在 PyTorch 中创建一个神经网络——让你的生活更简单

> 原文：<https://medium.com/coinmonks/create-a-neural-network-in-pytorch-and-make-your-life-simpler-ec5367895199?source=collection_archive---------0----------------------->

![](img/4f350a37242b188202c5d8f4973774ec.png)

“Black bike sits against dark brick wall” by [Darius Soodmand](https://unsplash.com/@dsoodmand?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

> 人生苦短，不能让神经网络变得复杂！

E 亚瑟王！实话实说吧。有许多图书馆可供我们用来满足深度学习的渴望。我也尝试过一些。刚开始的时候，我尝试了 tensorflow。然后我试了试 Keras。然后我尝试了 PyTorch。如果我对自己和所有的神——旧的 [*和新的*](https://images.google.com/imgres?imgurl=http%3A%2F%2F3.bp.blogspot.com%2F-wKYqGJyJXxc%2FVajqaq3Kk6I%2FAAAAAAAABVs%2FmQC1DTvLzVI%2Fs920%2FBanner%252BAGOT%252B2.jpg&imgrefurl=http%3A%2F%2Foldgodsandthenew.blogspot.com%2F2015%2F07%2Fblog-post.html&docid=UFkdfMrASAvO7M&tbnid=uE71uDhpRyczuM%3A&vet=1&w=920&h=364&source=sh%2Fx%2Fim)——完全诚实的话，我最喜欢 PyTorch。

我想我喜欢 PyTorch 胜过 Tensorflow 的原因是 PyTorch 非常*python 化*。它使用 python 的风格和功能，这使得理解和使用起来更加容易和简单。尤其对我来说，因为我主要用 Python 编码。Tensorflow 的问题在于，它需要你学习很多 Tensorflow 特有的术语。但这不是一个关于 PyTorch 和 Tensorflow 之间 15 世纪战争的帖子。这篇文章是关于如何在 PyTorch 中创建一个简单的神经网络。

> 我要教你如何生活。嗯……不完全是。但我会教你来这里的目的。

**更新:** *你可以在这里获得包含**[***的 Jupyter 笔记本完整代码***](https://github.com/J-Yash/Creating-a-simple-NN-in-pytorch) *。**

*我们将创建一个具有一个隐藏层和一个输出单元的简单神经网络。我们将在隐藏层使用 [ReLU 激活，在输出层使用](/alchemicai/activation-functions-demystified-661d1183f5f8)sigmoid 激活。*

*首先，我们需要导入 PyTorch 库。*

```
*import torch
import torch.nn as nn*
```

*然后，我们定义所有层的大小和批量大小*

```
*n_in, n_h, n_out, batch_size = 10, 5, 1, 10*
```

*现在，我们创建一些虚拟输入数据`x`和一些虚拟目标数据`y`。我们使用 PyTorch 张量来存储这些数据。PyTorch 张量可以像 NumPy 数组一样使用和操作，但是还有一个额外的好处，就是 PyTorch 张量可以在 GPU 上运行。但是在本教程中，我们将简单地在 CPU 上运行它们。虽然，把它们转移到 GPU 上是相当简单的。*

```
*x = torch.randn(batch_size, n_in)
y = torch.tensor([[1.0], [0.0], [0.0], [1.0], [1.0], [1.0], [0.0], [0.0], [1.0], [1.0]])*
```

*现在，对于主菜，我们将在一行代码中定义我们的模型。*

```
*model = nn.Sequential(nn.Linear(n_in, n_h),
                     nn.ReLU(),
                     nn.Linear(n_h, n_out),
                     nn.Sigmoid())*
```

*这就创建了一个类似于 input-> linear-> relu-> linear-> sigmoid 的模型。还有另一种方法来定义我们的模型，它用于定义更复杂和定制的模型。这是通过在类中定义我们的模型来完成的。你可以在这里 了解一下 [***。***](https://pytorch.org/tutorials/beginner/pytorch_with_examples.html#pytorch-custom-nn-modules)*

*现在，是时候构建我们的损失函数了。我们将使用均方误差损失。*

```
*criterion = torch.nn.MSELoss()*
```

*另外，不要忘记定义我们的优化器。我们在这个例子中使用随机梯度下降，学习率为 0.01。返回模型参数(权重和偏差)的迭代器。*

```
*optimizer = torch.optim.SGD(model.parameters(), lr=0.01)*
```

*现在，对于甜点，我们运行我们的梯度下降 50 个时代。这按顺序进行正向传播、损耗计算、反向传播和参数更新。*

```
*for epoch in range(50):
    # Forward Propagation
    y_pred = model(x) # Compute and print loss
    loss = criterion(y_pred, y)
    print('epoch: ', epoch,' loss: ', loss.item()) # Zero the gradients
    optimizer.zero_grad()

    # perform a backward pass (backpropagation)
    loss.backward()

    # Update the parameters
    optimizer.step()*
```

*`y_pred`从我们模型的正向传递中获取预测值。我们将它和目标值`y`一起传递给`criterion`，由其计算损失。然后，`optimizer.zero_grad()`将所有的渐变归零。我们需要这样做，以便以前的梯度不会继续积累。然后，`loss.backward()`是使用 PyTorch 亲笔签名功能的主要 PyTorch 魔法。Autograd 根据它动态创建的计算图自动计算所有梯度和所有参数。基本上，这是梯度下降的反向传递(反向传播)。最后，我们调用`optimizer.step()`，它使用新的梯度对所有参数进行一次更新。*

*仅此而已。我们已经在 PyTorch 中成功地训练了一个简单的两层神经网络，而且我们真的不需要通过大量的随机术语来完成它。PyTorch 保持它的甜蜜和简单，就像每个人都喜欢的那样。*

*如果您想了解更多关于 PyTorch 的知识，并想更深入地了解它，可以看看 PyTorch 的官方文档和教程。他们确实写得很好。你可以在这里找到它们[](https://pytorch.org/tutorials/)*。**

**我希望您觉得这篇文章很有用，并且比以前对 PyTorch 有了更好的了解。**

> **“害怕它。逃离它。错误仍然存在—灭霸”**

***如果你想了解更多关于深度学习的知识，那么* ***跟我来*** *。我会在不断学习的同时，不断发布有用的深度学习教程和文章。***

*****有任何问题、反馈或批评吗？在*** [***上留言评论或者联系我***](http://twitter.com/iamjyash) ***。*****

> **加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)**

## **另外，阅读**

*   **[复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)**
*   **[网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)**
*   **[密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)**
*   **[有哪些交易信号？](https://coincodecap.com/trading-signal) | [比特斯坦普 vs 比特币基地](https://coincodecap.com/bitstamp-coinbase)**
*   **[ProfitFarmers 回顾](https://coincodecap.com/profitfarmers-review) | [如何使用 Cornix Trading Bot](https://coincodecap.com/cornix-trading-bot)**
*   **[如何在势不可挡的域名上购买域名？](https://coincodecap.com/buy-domain-on-unstoppable-domains)**
*   **[印度的秘密税](https://coincodecap.com/crypto-tax-india) | [altFINS 审查](https://coincodecap.com/altfins-review) | [Prokey 审查](/coinmonks/prokey-review-26611173c13c)**
*   **[最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)**
*   **[面向开发者的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)**
*   **最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)**
*   **[杠杆代币终极指南](/coinmonks/leveraged-token-3f5257808b22)**