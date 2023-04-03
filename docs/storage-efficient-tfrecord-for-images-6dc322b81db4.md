# 图像的高效存储 TFRecord

> 原文：<https://medium.com/coinmonks/storage-efficient-tfrecord-for-images-6dc322b81db4?source=collection_archive---------2----------------------->

![](img/77ae5b58d835fcbabbbdaa831d610b4d.png)

“Binders and boxes on shelves in a large archive” by [Samuel Zeller](https://unsplash.com/@samuelzeller?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

机器和深度学习的核心支柱之一是训练模型时使用的数据，因为它们是算法能够归纳和创建相关模型的来源。使用 Tensorflow 时，有几种方法可以向这些模型提供数据，但推荐的方法是使用 TFRecord 格式，它基本上存储地图中结构化数据的字节数组。这些图中的每一个都可以很容易地解释为一个特征或示例，并且包含所有相关的信息，这些信息将在以后的训练和测试步骤中使用。

大多数关于 TFRecord 的帖子直接创建了这些功能，却没有对存储进行必要的关注。当我尝试将 TFRecord 文件用于图像时，我遇到了有关大文件存储的问题，因为这些 TFRecord 文件开始增长到原始数据大小的 10 倍，这在处理大型数据集时会变得非常烦人。

来自 tensorflow repository 的这个封闭问题清楚地表明了这个问题，但也提供了一个非常有效的解决方案。我们将在创建 TFRecord 功能之前进行编码，而不是盲目地存储图像。

(所有片段都是基于一个工作中的 TFRecord 构建脚本，可以在[这个资源库](https://github.com/tinenbruno/image-tfrecord-builder)中找到，并且是基于 [Kwot Sin](https://kwotsin.github.io/tech/2017/01/29/tfrecords.html) 的工作。)

## 第一步。组织影像数据集

首先，我们需要组织我们的数据集，使我们更容易处理它的类。实现这一点的一种方法是按以下方式构建它:

```
> base_dir
  > classname 1
    > image 1
    > image 2
    > ...
  > classname 2
    > image 1
    > image 2
    > ...
```

使用这种结构将允许我们简单地遍历文件夹来收集关于类名和图像路径的所有信息。

## 第二步。分割数据集

我们将在数据集中创建两种类型的分割。第一个是评估您的机器学习模型的核心，是关于将数据集划分为训练和测试子集。顾名思义，训练数据集将用于模型的训练步骤，测试数据集将用于验证该模型。
我们将创建的第二个分割是用于训练和测试数据集的碎片。正如这里所说的，分片可以加快你的阅读速度，而且通常最好不要有千兆字节大小的文件。如果数据集开始变得非常大，这将非常重要。

## 第三步。创建 TFRecord 文件

我们将对使用 OpenCV 加载的每个图像进行编码，然后作为一个特性存储在 TFRecord 文件中。例如，我们可以使用`cv2.imencode`将图像编码为 JPEG。

*编辑:注意，我们这里使用的是 OpenCV，而不是 raw 文件打开。使用 OpenCV 可以在构建特征图之前对图像进行预处理。如果你想要的只是将图像存储在 TFRecord 文件中，那么你可以考虑使用* `*with open(filename, 'rb'):*` *。感谢*[*Andrey Kite Gorin*](https://medium.com/u/876d247489fb?source=post_page-----6dc322b81db4--------------------------------)*的输入。*

```
encoded_image_string = cv2.imencode(‘.jpg’, image)[1].tostring()
```

然后，我们可以简单地使用这个编码图像串来生成表示我们的特征的地图。因为我们的目标是获得一个精简的记录，所以我们将只存储后面步骤所需的最少信息。

```
label = tf.compat.as_bytes(example['classname'])
image = tf.compat.as_bytes(encoded_image_string)feature = {
  'train/label':_bytes_feature(label),
  'train/image':_bytes_feature(image)
}

tf_example = tf.train.Example(
  features = tf.train.Features(feature=feature)
)writer.write(tf_example.SerializeToString())
```

## 从 TFRecords 中读取

我可以在这里结束这篇文章，但是还有一件重要的事情要说:如何从 TFRecord 文件中读取。如前所述，TFRecord 以结构化的方式存储特性，这是在构建 TFRecord 文件时定义的。然后，当从 TFRecord 文件中读取时，准确地知道正在读取的特征的结构是很重要的。如果您遵循本文的示例，那么当读取 TFRecord 文件时，我们将期望它被解析如下:

*编辑:正如 Andrey Kite Gorin*[*所评论的，在本参考中，在这种情况下使用 FixedLenFeature 而不是 VarLenFeature，因为我们总是有一个包含* ***的特征，一个图像*** *，或者换句话说，一个数组。这种情况与字节数组的大小无关。*](https://medium.com/u/876d247489fb?source=post_page-----6dc322b81db4--------------------------------)

```
features = tf.parse_single_example(
  serialized_example,
  features={
    'train/label': tf.FixedLenFeature(tf.string),
    'train/image': tf.FixedLenFeature(tf.string)
  }
)
```

我们还需要解码 JPEG 编码的图像，这可以使用:

编辑:正如安德烈·凯特·戈林评论的那样，在这种情况下使用`tf.image.decode_jpeg()`可能是个更好的主意。

```
cv2.imdecode(np.fromstring(img_data, dtype=np.uint8), -1
```

## 结论

最后，我们将得到一个 TFRecord 格式的数据集，其大小接近原始数据。请记住，与训练时间相比，解码时间通常是微不足道的，这可能是一个节省存储费用的好办法。

感谢安德烈·凯特·戈林的贡献！

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [杠杆代币](/coinmonks/leveraged-token-3f5257808b22)终极指南