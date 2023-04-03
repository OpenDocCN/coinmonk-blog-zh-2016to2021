# 使用 Tensorflow 和 python 的机器学习中的随机与小批量训练

> 原文：<https://medium.com/coinmonks/stochastic-vs-mini-batch-training-in-machine-learning-using-tensorflow-and-python-7f9709143ee2?source=collection_archive---------2----------------------->

用最简单的话来说，随机训练是一次对一个随机选择的样本进行训练，而小批量训练是对全部样本中的一部分进行训练。

> 根据数据的大小，小批量的大小可能会有所不同。在后面的教程中，我将向您展示一些挑选小型批量的最佳实践。

注意:我们将使用 Tensorflow 和 python 来实现和可视化这些培训。让我们从随机训练开始。