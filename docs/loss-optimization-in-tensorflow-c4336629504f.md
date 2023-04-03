# 张量流中的损耗优化

> 原文：<https://medium.com/coinmonks/loss-optimization-in-tensorflow-c4336629504f?source=collection_archive---------1----------------------->

![](img/c22db78bec5e0697ca584d4673ac7810.png)

Optimization is like trying to find the lowest point in a terrain such as this

机器学习总是有一个阶段，你做出预测，然后将你的预测与地面事实进行比较。这是*训练*步骤。将你的预测与实际情况进行比较的算法是*损失*函数。下一步是你*优化*损失，尝试减少损失。虽然这不是唯一的目标，但却是最常用的目标。

大多数机器学习的学生被教导从头开始实现批量梯度下降、小批量梯度下降和随机梯度下降算法。在了解工作原理后，只有研究人员可能会从头开始继续实现。

三种算法都有各自的优点，更重要的是，也有各自的局限性。我不会在这里详述，但是你可以在这里阅读 Sebastian Ruder 的精彩文章。

在本文中，我将向您展示如何在 TensorFlow 中的自定义函数上实现黑盒优化算法。让我们从导入所需的库开始。

```
import pandas as pd
import numpy as np
import tensorflow as tf
from tensorflow.python.ops import confusion_matrix
from tensorflow.python.ops import math_ops
```

我们引入通常的疑点，和两个不常见的疑点来计算均方误差。本例中使用的示例数据集来自 Kaggle 上的波士顿房价预测。

```
train_df = pd.read_csv('./train.csv', index_col='ID')
```

让我们从提取地面真相开始。我们需要重塑它来得到一个矩阵或数组。

```
y = train_df['medv'].values
y = y.reshape(-1, 1)
```

对于我们的预测器，我们将添加一个常数列，这样我们就不需要在函数中做任何加法。我们也需要从中提取我们的数组。

```
train_df['constant'] = 1
columns = ['constant', 'rm', 'zn', 'indus']
x = train_df[columns].values
```

因为我们和 TensorFlow 合作，所以需要和 tensors 合作。更重要的是，为了优化我们的函数，我们需要指定我们的权重是可训练的。我们在初始化期间这样做。

```
w = tf.Variable([[0],[0],[0],[0]], trainable=True, dtype=tf.float64) # this is needed to make the weights trainable
x = tf.convert_to_tensor(x)
y = tf.convert_to_tensor(y)
```

接下来，我们实现我们的预测。

```
y_pred = tf.matmul(x, w)
```

然后，我们声明我们的损失函数。TensorFlow 在 TF . loss 中提供了许多标准损失函数。

```
mse = tf.losses.mean_squared_error(y, y_pred) # the loss function
```

接下来，我们实例化我们的优化器。这里，我们利用了 adam 优化器。tf.train 中提供了许多优化器。

```
adam = tf.train.AdamOptimizer(learning_rate=0.3) # the optimizer
```

我们需要一种方法在梯度下降的每一步调用优化函数。我们通过将最小化调用分配给一个句柄来实现这一点。

```
a = adam.minimize(mse, var_list=w) # this runs one step of gradient descent
```

我们需要知道我们的 MSE 是什么的方法。虽然这在 TensorFlow 中存在，但它一直抛出异常，所以我只是在这里实现了它。

```
l, p = confusion_matrix.remove_squeezable_dimensions(y, y_pred)
s = math_ops.square(p - l)
mean_t = math_ops.reduce_mean(s)
print(mean_t)
```

就这样，我们结束了。当然，这是张量流，我们需要一个会话。

```
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    for i in range(200):
        sess.run(a) # run for the number of training steps
    w = sess.run(w)

    print(w) # this will output our current weights after training
    e_val = sess.run(mean_t) # compute our MSE
    print(e_val) # print our MSE.
```

我希望这对你的工作或研究有用。编码快乐！

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   杠杆代币的终极指南