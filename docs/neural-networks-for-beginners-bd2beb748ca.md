# 初学者的神经网络

> 原文：<https://medium.com/coinmonks/neural-networks-for-beginners-bd2beb748ca?source=collection_archive---------6----------------------->

好吧，如果你已经开始机器学习，你应该听说过神经网络！那么什么是神经网络呢？基本上，它只是目前机器学习中最有效和最流行的算法之一。它的结构就像我们大脑中的神经元一样！有趣吧？

所以，我们写一个短代码来预测一个人会不会献血。这段代码使用了 Tensorflow(谷歌的一个机器学习和深度学习的库)。我强烈建议您使用在其中使用张量流估计器的代码，除非您使用 keras 和 sci-kit learn 的内置模型。这是因为这样可以节省大量时间，而且有预先写好的代码。在神经网络中编写正向传播和反向传播算法变得非常忙乱！所以继续使用这些代码。好了，够了！开始吧！

所以让我们从导入一些库开始。Numpy 用于数值计算，pd 用于处理数据集！

```
**import** **tensorflow** **as** **tf** 
**import** **pandas** **as** **pd** 
**import** **numpy** **as** **np** 
```

那么，现在让我们定义我们的参数。training_size 比例是拆分训练和测试数据(记住！从不在相同的训练和测试数据上训练)。然后我们洗牌，因为如果我们不这样做，模型就有可能对所有倾向于献血的人进行训练，所以它永远不会知道那些永远不会献血的人。只需将准确度分数重置为 0，以确保没有垃圾值。隐藏单元表示有 3 个隐藏层，分别有 10、20、10 个节点。Steps_spec 给出了训练模型的步骤数。将 tmp_dir_spec 配置到您想要保存模型的所有检查点的目录。确保数据在您的文件夹中。ipynb 文件是。你可以从下面的我的 GitHub 下载笔记本和数据集。根据您的需要配置输入和输出功能。https://github.com/kaiwalya4850/Transfusio

```
training_set_size_portion = 0.8 
do_shuffle = **True** 
accuracy_score = 0 
hidden_units_spec = [10,20,10] 
n_classes_spec = 2 
steps_spec = 200000 
epochs_spec = 15 
tmp_dir_spec = “C:/Users/XYZ/Desktop/ML/Sourcecodes/NN/ckpt_tranfusion” 
file_name = “transfusion.csv” 
features = [‘recency’,’tinv’,’monetary’,’t’] 
*# Here’s the label that we want to predict — it’s also a column in # the CSV* labels = [‘op’]
```

好了，现在让我们来玩数据集吧！使用下面的代码，您应该能够可视化您的数据。尝试类似 my_data.head(10)的方法来获取前 10 列。我们将数据集的名称保留为 my_data，而不是 transfusion。

```
my_data = pd.read_csv("transfusion.csv",dtype=float) 
my_data.head()
```

现在，让我们只是随机数据，以避免任何问题，在去皮重！

```
**if** do_shuffle:
 randomized_data = my_data.reindex(np.random.permutation(my_data.index))
**else**:
 randomized_data = my_data
```

您可以通过编写 my_data.head()来再次可视化您的数据，以确保一切都是正确随机化的！

现在，我们将训练和测试数据分离出来，以便进一步计算。并且为训练特征和标签而构建。

```
total_records = len(randomized_data) 
training_set_size = int(total_records * training_set_size_portion) test_set_size = total_records = training_set_size*# Build the training features and labels*training_features = randomized_data.head(training_set_size[features].copy()
training_labels = randomized_data.head(training_set_size[labels].copy()
print(training_features.head())
print(training_labels.head())
```

现在我们做同样的测试。如果出现“缩进”错误，只需清除空格。他们会被解决的！

```
*#Build the testing features and labels*
testing_features = randomized_data.tail(test_set_size)[features].copy()
testing_labels = randomized_data.tail(test_set_size)[labels].copy()
```

为功能栏编写:

```
feature_columns = [tf.feature_column.numeric_column(key) **for** key **in** features]
```

现在，来看主要的东西，tf.estimator。这是原因，也是代码的一部分，它为我们做一切事情，就像神经网络中的反向传播算法。DNNClassifier 用于神经网络，你会发现线性回归和逻辑回归的 LinearRegressor。我将把我的 GitHub 链接放在这篇文章的末尾，在那里我使用不同的 tf 估算器进行了训练。

```
classifier = tf.estimator.DNNClassifier(
    feature_columns=feature_columns, 
    hidden_units=hidden_units_spec, 
    n_classes=n_classes_spec,
    model_dir=tmp_dir_spec)
```

定义训练输入函数，这将被其他函数调用以开始训练。

```
train_input_fn = tf.estimator.inputs.pandas_input_fn(x=training_features,
                                    y=training_labels,
                                    num_epochs=epochs_spec,
                                    shuffle=**True**)
```

现在开始实际训练。它没有完成我之前在电脑中定义的所有步骤，所以尝试运行多次，这样你会损失更少。

```
*# Train the model using the classifer.* classifier.train(input_fn=train_input_fn, steps=steps_spec)
```

好吧！训练到此结束。现在是时候用之前定义的测试数据来测试和评估模型了。

```
*# Define the test input function*
test_input_fn = tf.estimator.inputs.pandas_input_fn(
                    x=testing_features, 
                    y=testing_labels, 
                    num_epochs=epochs_spec, 
                    shuffle=**False**)
```

混淆矩阵中的值对于查看您的模型有多好非常方便和有用。下面的代码是混乱矩阵。这将打印 AUC(曲线下面积)、精度、召回、损失、训练准确度、测试准确度。

```
*# Evaluate accuracy.*
accuracy_score = classifier.evaluate(input_fn=test_input_fn)["accuracy"]
print("Accuracy = **{}**".format(accuracy_score))
```

现在，让我们创建预测集。仔细看看，我是如何编写输入特性和输出特性的。相应地改变。

```
*# Create a prediction set -- this is a list of input features that you want to classify*
prediction_set = pd.DataFrame({'recency':[2, 1], 'monetary':[4,3],'tinv':[3,2],'t':[5,4],'op':[4,5]})
```

现在，让我们预测输入函数。

```
predict_input_fn = tf.estimator.inputs.pandas_input_fn(
                      x=prediction_set, 
                      num_epochs=1, 
                      shuffle=**False**)
```

现在让我们得到一个预测函数的列表。当您想要在 android 或任何其他平台上实际服务您的模型时，这些步骤变得非常重要。因为要把它们转换成。tflite 格式，你需要。这些代码行可以生成 pbtxt 文件。

好吧！就是这样！这是你的第一个神经网络！改变数据集，调整参数，享受！如果你想要更多的带有 tf 估算器的代码或者从头开始，GitHub 链接在下面。我会确保用不同的算法更新所有的模型。[https://github.com/kaiwalya4850](https://github.com/kaiwalya4850)。

感谢阅读！