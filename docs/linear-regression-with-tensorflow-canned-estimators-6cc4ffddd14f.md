# 张量流罐装估计量的线性回归

> 原文：<https://medium.com/coinmonks/linear-regression-with-tensorflow-canned-estimators-6cc4ffddd14f?source=collection_archive---------2----------------------->

![](img/0bbc07179ae62937ed4539113b0d939e.png)

Linear Regression Illustration

线性回归是一种经常被教授给机器学习的第一次实践者的算法。使用线性回归教授梯度下降和损失的基本原理。

本教程是关于使用 TensorFlow 实现线性回归。目标是通过向您展示如何在 TensorFlow 中使用固定估计量来节省您的时间。固定估算器是一个高级 API，不同于要求你自己编程的低级 API。

我们将从导入所需的库开始。

```
import numpy as np
import pandas as pd
import tensorflow as tf
from sklearn.model_selection import train_test_split
```

我们将为此使用的数据集来自 Kaggle [这里](https://www.kaggle.com/c/boston-housing)上的波士顿房价预测组合。让我们导入我们的训练数据集。

```
train_df = pd.read_csv('./train.csv')
```

您需要检查数据帧，找出列名和类型。

使用 TensorFlow 时的一个概念是*特征列*。在实例化时，我们需要将我们的特性列传递给我们的固定估算器。让我们创建我们的专题专栏。

```
crim = tf.feature_column.numeric_column('crim', dtype=tf.float64, shape=())
zn = tf.feature_column.numeric_column('zn', dtype=tf.float64, shape=())
indus = tf.feature_column.numeric_column('indus', dtype=tf.float64, shape=())
chas = tf.feature_column.numeric_column('chas', dtype=tf.int64, shape=())
nox = tf.feature_column.numeric_column('nox', dtype=tf.float64, shape=())
rm = tf.feature_column.numeric_column('rm', dtype=tf.float64, shape=())
age = tf.feature_column.numeric_column('age', dtype=tf.float64, shape=())
dis = tf.feature_column.numeric_column('dis', dtype=tf.float64, shape=())
rad = tf.feature_column.numeric_column('rad', dtype=tf.int64, shape=())
tax = tf.feature_column.numeric_column('tax', dtype=tf.int64, shape=())
ptratio = tf.feature_column.numeric_column('ptratio', dtype=tf.float64, shape=())
black = tf.feature_column.numeric_column('black', dtype=tf.float64, shape=())
lstat = tf.feature_column.numeric_column('lstat', dtype=tf.float64, shape=())
```

在实例化过程中，我们需要将我们的特性列作为一个列表传递。所以，让我们来创造它。

```
feature_cols = [crim, zn, indus, chas, nox, rm, age, dis, rad, tax, ptratio, black, lstat]
```

通常，在处理数据时，我们会创建一个训练集和一个验证集。那么，让我们在这里也这样做。

```
feature_names = ['crim', 'zn', 'indus', 'chas', 'nox', 'rm', 'age', 'dis', 'rad', 'tax', 'ptratio', 'black', 'lstat']
label_name = 'medv'features_ndarray = train_df[feature_names]
label_ndarray = train_df[label_name]X_train, X_test, y_train, y_test = train_test_split(features_ndarray, label_ndarray, random_state=0, test_size=0.3)
```

如果您一直在使用 sklearn，您将习惯于简单地将您的数据传递给一个训练函数。在 TensorFlow 中，您使用输入函数。我们需要为训练和验证创建输入函数。

```
def train_input():
    _dataset = tf.data.Dataset.from_tensor_slices(({'crim': X_train['crim'], 
                                                   'zn': X_train['zn'], 
                                                   'indus': X_train['indus'],
                                                   'chas': X_train['chas'],
                                                   'nox': X_train['nox'],
                                                   'rm': X_train['rm'],
                                                   'age': X_train['age'],
                                                   'dis': X_train['dis'],
                                                   'rad': X_train['rad'],
                                                   'tax': X_train['tax'],
                                                   'ptratio': X_train['ptratio'],
                                                   'black': X_train['black'],
                                                   'lstat': X_train['lstat']
                                                  }, y_train))
    dataset = _dataset.batch(32)
    iterator = dataset.make_one_shot_iterator()
    features, labels = iterator.get_next()
    return features, labels
```

创建 _dataset 的方法有很多种。因为我们用 pandas 加载数据，所以我们有一个数据框架。因此，我们将使用 *from_tensor_slices()* 来创建数据集。此功能在 TensorFlow 1.8 发布的 tf.data 模块中提供。特别注意这个功能很重要。第一个参数是一个字典。您需要将系列数据作为字典的值进行传递。第二个参数是代表训练数据中标签的序列。

下一个非常重要的步骤是，我们必须批量获取数据。您可以将批量大小作为参数传入。如果没有这个对 *batch()* 的调用，你会得到一些关于 size()的有趣错误。

我们还要做的一件事是创建一个迭代器。我们使用 *make_one_shot_iterator()* 来实现这一点。

我们通过返回 *iterator.get_next()* 来结束我们的函数。

我们需要一个类似的评估数据函数。

```
def val_input():
    _dataset = tf.data.Dataset.from_tensor_slices(({'crim': X_test['crim'], 
                                                   'zn': X_test['zn'], 
                                                   'indus': X_test['indus'],
                                                   'chas': X_test['chas'],
                                                   'nox': X_test['nox'],
                                                   'rm': X_test['rm'],
                                                   'age': X_test['age'],
                                                   'dis': X_test['dis'],
                                                   'rad': X_test['rad'],
                                                   'tax': X_test['tax'],
                                                   'ptratio': X_test['ptratio'],
                                                   'black': X_test['black'],
                                                   'lstat': X_test['lstat']
                                                  }, y_test))
    dataset = _dataset.batch(32)
    iterator = dataset.make_one_shot_iterator()
    features, labels = iterator.get_next()
    return features, labels
```

我们现在准备实例化我们的固定估算器。回想一下我们之前创建的特性列列表，我们将它传递给对 *LinearRegressor()* 的调用。我们可以传递许多不同的参数，比如要使用的优化器。此时我们将使用默认值。

```
estimator = tf.estimator.LinearRegressor(feature_columns=feature_cols)
```

我们现在准备训练我们的估计器。

```
estimator.train(input_fn=train_input, steps=None)
```

训练完成后，我们将准备评估我们的模型。

```
train_e = estimator.evaluate(input_fn=train_input)
test_e = estimator.evaluate(input_fn=val_input)
```

我们准备进行推理。我们得到了这个调用的迭代器。

```
preds = estimator.predict(input_fn=val_input)
```

我们需要对此进行迭代，并转换成一个 numpy 数组来获得结果。

```
predictions = np.array([item['predictions'][0] for item in preds])
```

仅此而已。我们在一个数组中有我们的预测。

我希望你觉得这是有用的。