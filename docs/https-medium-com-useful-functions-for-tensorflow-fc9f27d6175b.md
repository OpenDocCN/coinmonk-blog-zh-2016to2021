# 张量流的有用函数

> 原文：<https://medium.com/coinmonks/https-medium-com-useful-functions-for-tensorflow-fc9f27d6175b?source=collection_archive---------6----------------------->

![](img/bd2ba479b39778a5ac6e9e6434c04417.png)

Tensorflow

Tensorflow 是一个非常棒的深度学习库，提供了很多功能。Tensorflow 提供了许多特性，这使得开发深度神经网络变得更加容易。

在本帖中，我们将看到一些有用的预定义和用户定义的方法，这些方法可能对你使用 tensorflow 有所帮助。

# 内置方法

## 1. [tf.zeros_like](https://www.tensorflow.org/api_docs/python/tf/zeros_like)

该函数将一个张量作为输入，并返回一个值为零、形状和类型与输入张量相同的张量。
`tensor = tf.constant([[1, 2, 3], [4, 5, 6]])
tf.zeros_like(tensor) # [[0, 0, 0], [0, 0, 0]]` 该功能在从给定输入图像创建黑色图像的情况下很有用。
*如果想明确给出形状，可以使用 tf.zeros。如果你想初始化 1 而不是 0，你可以使用 tf.ones_like。

## **2。** [**tf.pad**](https://www.tensorflow.org/api_docs/python/tf/pad)

在具有常数值的张量周围添加指定的填充，从而增加张量的维数。
`t = tf.constant([[1, 2, 3], [4, 5, 6]])
paddings = tf.constant([[1, 1,], [2, 2]])
# 'constant_values' is 0.
# rank of 't' is 2.
tf.pad(t, paddings, "CONSTANT") # [[0, 0, 0, 0, 0, 0, 0],
# [0, 0, 1, 2, 3, 0, 0],
# [0, 0, 4, 5, 6, 0, 0],
# [0, 0, 0, 0, 0, 0, 0]]` 它可以用来在图像周围添加边框。

## 3.[TF . enable _ eager _ execution](https://www.tensorflow.org/api_docs/python/tf/enable_eager_execution)

这有助于您在执行张量流代码时运行它们。使用急切执行，您不需要构建图形并在会话中运行它。你可以在这里阅读更多关于如何使用急切执行[的内容。
*急切执行需要是导入 tensorflow 后的第一条语句。](https://towardsdatascience.com/eager-execution-tensorflow-8042128ca7be)

# 用户定义的函数

下面是我在代码中使用的一些函数。这些功能无疑使许多事情变得更容易。

## 1.可视化卷积图层的权重

如果你想看到卷积滤波器的样子，你可以使用下面的函数。

```
def plot_conv_weights(weights, input_channel = 0):
#Weights are the filter values which you need to pass for visualization
#Input Channel is the channel for which you want all the filters w = sess.run(weights)

 w_min = np.min(w)
 w_max = np.max(w)

 num_filters = w.shape[3]

 num_grids = math.ceil(math.sqrt(num_filters))

 fig, axes = plt.subplots(num_grids, num_grids)
 fig.subplots_adjust(hspace = 0.3, wspace= 0.3)
 for i, ax in enumerate(axes.flat):
 if i<num_filters:
 img = w[:,:, input_channel, i]

 ax.imshow(img, vmin=w_min, vmax=w_max, interpolation=’nearest’, cmap = ‘seismic’)
 x_label = “Filter {0}”.format(i+1)
 ax.set_title(x_label)
 ax.set_xticks([])
 ax.set_yticks([])

 plt.show()
```

## 2.获取卷积层的权重

要获得权重值，您可以使用 below 函数，只需传递层的名称。

```
def get_weights_variable(layer_name):
  with tf.variable_scope(layer_name, reuse = True):
    variable = tf.get_variable('kernel')

  return variable
```

## 3.获取卷积层的输出

如果您想了解过滤器是如何激活的，或者图层的输出是什么样的。

```
def plot_conv_output(layer, image):
  feed_dict = {x: [image]}

  values = sess.run(layer, feed_dict)
  # Number of filters used in the convolution layer.
  num_filters = values.shape[3] # Number of grids to plot.
  # Rounded-up, square-root of the number of filters.
  num_grids = math.ceil(math.sqrt(num_filters)) # Create figure with a grid of sub-plots.
  fig, axes = plt.subplots(num_grids, num_grids) # Plot the output images of all the filters.
  for i, ax in enumerate(axes.flat):
      # Only plot the images for valid filters.
      if i<num_filters:
          # Get the output image of using the i'th filter.
          img = values[0, :, :, i] ax.imshow(img, interpolation='nearest', cmap='binary') ax.set_xticks([])
      ax.set_yticks([]) plt.show()
```

这是我认为在使用 tensorflow 时可能有用的几个函数。如果你使用了一些你认为可能有用的功能，请把它们写在评论里或者发邮件给我。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)