# 用 SKLearn 正则化线性模型

> 原文：<https://medium.com/coinmonks/regularization-of-linear-models-with-sklearn-f88633a93a2?source=collection_archive---------1----------------------->

![](img/1311fc9b088c8cfb0f03d54d7763cb6a.png)

An overfit model

线性模型通常是训练模型的良好起点。然而，许多数据集并不显示自变量和因变量之间的线性关系。因此，经常需要创建多项式模型。然而，这些模型通常容易过度拟合。减少多项式模型中过拟合的一种方法是通过使用正则化。

让我们首先构建一个基线模型来确定需要的改进。我们将利用流行的波士顿房屋数据集，该数据集可在 Kaggle [这里](https://www.kaggle.com/c/boston-housing)获得。

让我们导入必要的库并加载我们的训练数据集。

让我们将数据分为训练集和验证集。我们将拿出 30%的数据进行验证。我们将使用随机状态使我们的实验可重复。

让我们通过训练线性回归模型来建立基线。

上面的模型应该给我们一个大约 72%的训练精度和测试精度。我们也应该得到大约 4.587 的 RMSE。我们训练的下一个模型应该比这个模型具有更高的准确度分数和更低的 RMSE。

我们需要设计新的功能。具体来说，我们需要通过将我们的单个特征提升到选定的幂来创建多项式特征。幸运的是，scikit-learn 对此有一个实现，我们不需要手动完成它。

我们想做的另一件事是标准化我们的数据。这将我们的数据缩小到 0 和 1 之间的范围。这是为了让我们在计算幂时，能够使用合理的数字。

最后，因为我们需要在我们的训练、验证和测试集上执行相同的操作，所以我们将引入一个管道。这将使我们能够对我们的过程进行管道化，从而重复执行相同的步骤。

总而言之，我们将缩放数据，然后创建多项式特征，然后训练线性回归模型。

运行我们的代码后，我们将获得大约 94.75%的训练准确率和 46.76%的测试准确率。这是过度拟合的标志。这通常不是一个理想的特性，但这正是我们所希望的。

我们现在将正则化应用到我们的新数据。

# l2 正则化或岭回归

为了理解岭回归，我们需要提醒自己在梯度下降过程中，当我们的模型系数被训练时发生了什么。在训练期间，我们的初始权重根据梯度更新规则使用学习率和梯度进行更新。岭回归增加了更新的代价，结果缩小了权重的大小。这在 scikit-learn 中实现为一个名为 Ridge 的类。

我们将创建一个新的管道，这一次使用山脊。我们将通过传入参数α来指定正则化强度。这个值可以非常小，比如 0.1，也可以是你想要的大小。alpha 值越大，模型显示的方差越小。

通过执行代码，我们应该有大约 91.8%的训练准确率，以及大约 82.87%的测试准确率。这是对我们基线线性回归模型的改进。

让我们试试别的东西。

# l1 正则化或套索回归

通过创建多项式模型，我们创建了额外的功能。我们需要问自己的问题是，我们的哪些特性与我们的模型相关，哪些不相关。

l1 正则化试图通过将某些系数的值降低到 0 来回答这个问题。这消除了我们模型中最不重要的特性。我们将创建一个类似上面的管道，但使用套索。您可以随意使用 alpha 值，范围从 0.1 到 1。

上面的代码应该给我们 84.8%的训练准确率和 83%的测试准确率。这是一个比我们之前培训的模型更好的模型。

此时，您可以通过找到 RMSE 来评估您的模型。不要忘记阅读我们使用的所有文档。

我希望这个教程对你有用。下次见。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [Pionex 双投](https://coincodecap.com/pionex-dual-investment) | [AdvCash 审核](https://coincodecap.com/advcash-review) | [光宗耀祖审核](https://coincodecap.com/uphold-review)
*   [面向开发者的 8 个最佳加密货币 APIs】](https://coincodecap.com/best-cryptocurrency-apis)
*   [拥护卡审核](https://coincodecap.com/uphold-card-review) | [信任钱包 vs MetaMask](https://coincodecap.com/trust-wallet-vs-metamask)
*   [赢取注册奖金——10 大最佳加密平台](https://coincodecap.com/earn-sign-up-bonus)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   杠杆代币的终极指南