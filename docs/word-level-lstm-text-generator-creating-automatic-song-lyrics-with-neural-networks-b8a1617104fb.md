# 单词级 LSTM 文本生成器。用神经网络生成自动歌词。

> 原文：<https://medium.com/coinmonks/word-level-lstm-text-generator-creating-automatic-song-lyrics-with-neural-networks-b8a1617104fb?source=collection_archive---------0----------------------->

![](img/6cd7a8920f9995ee829bbef63ea352b4.png)

我开始用非技术性的聊天来谈论这个项目，谈论我对墨西哥班达音乐(西班牙语)的 5000 首歌词(超过 500 万字符)进行的分析。

 [## 5，000 莱特拉德墨西哥州(班达)

### 作为一个个人项目的一部分，在他工作的时候，他记录了一个文集…

medium.com](/@monocasero/analizando-más-5-000-letras-de-canciones-de-música-regional-mexicana-banda-8f6aecceb5b4) 

我决定用英语来写下面的故事/教程，因为这样自然可以接触到更多的读者。

完整的代码和语料库可以在[这个 github 库](https://github.com/enriqueav/lstm_lyrics)找到。

**更新:**第二部可用[此处](/coinmonks/text-classifier-with-keras-tensorflow-using-recurrent-neural-networks-ad63dd5fc316)。我用单词嵌入来解释这个训练。"*单词嵌入提供了单词及其相对含义的密集表示."*

# 快速背景

> **长短期记忆** ( **LSTM** )单位(或块)是一个[递归神经网络](https://en.wikipedia.org/wiki/Recurrent_neural_network) (RNN)的层的一个构建单位。由 LSTM 单元组成的 RNN 通常被称为 LSTM 网络。一个普通的 LSTM 单元由一个**单元**、一个**输入门**、一个**输出门**和一个**遗忘门**组成。来源[维基百科](https://en.wikipedia.org/wiki/Long_short-term_memory)。

rnn 可用于进行预测，或从序列数据中学习并生成类似的数据。

像许多在线文本生成的例子一样，我从 keras-team 的[例子](https://github.com/keras-team/keras/blob/master/examples/lstm_text_generation.py)和这个[项目中获得灵感，生成类似特朗普的推文](https://towardsdatascience.com/yet-another-text-generation-project-5cfb59b26255)。

主要的区别是，我创建的文本生成器是在单词级而不是字符级工作的，就像前面的例子一样。此外，由于训练集不适合内存(特别是如果发送到 GPU)，我需要为 **fit** 和 **evaluate** Keras 函数创建一个数据生成器。

*我不得不承认，在做一个快速的谷歌搜索之前，我一直在做这个项目的版本。我终于做到了，发现* [*这个项目*](https://github.com/rdcolema/word-level-rnn-for-text-generation/blob/master/word_gen.py) *，也是基于相同的脚本，因此有点类似于我的版本:*

# 算法的解释

这个想法是用许多单词序列和目标 *next_word 来训练 RNN。*作为一个简化的例子，如果每个句子是一个五个单词的列表，那么目标是一个只有一个元素的列表，指示哪个是原文中下面的单词:

```
>>> sentences[0]['put', 'a', 'gun', 'against', 'his']>>> next_words[0]'head'>>> sentences[1]['a', 'gun', 'against', 'his', 'head']>>> next_words[1]'pulled'
```

我们实际上并不发送字符串，而是发送一个可能单词的字典中的单词的矢量化表示(稍后会详细介绍)。这个想法是，在许多代之后，RNN 将学习如何书写语料库的“风格”,试图调整网络的权重，以预测给定 N 个先前单词序列的下一个单词。

# 文本语料库

正如我在另一个故事中解释的那样，语料库包含超过 100 万个单词中的超过 500 万个字符。获取文本只是问题的开始，因为与任何其他机器学习项目一样，有必要分析、清理和执行这些数据的一些预处理。

我现在不会进入细节(可能是另一个故事)，但至少可以说，数据是**脏的。**成千上万的错别字、拼写错误、俚语、不正确的标点符号、 [spanglish](https://en.wikipedia.org/wiki/Spanglish) 等等。

现在谈谈算法。

# 阅读文集，拆分成单词

第一步，阅读语料库，拆分成单词。

```
corpus = sys.argv[1] # first command line arg
with io.open(corpus, encoding='utf-8') as f:
    text = f.read().lower().replace('\n', ' \n ')
print('Corpus length in characters:', len(text))

text_in_words = [w for w in text.split(' ') if w.strip() != '' or w == '\n']
print('Corpus length in words:', len(text_in_words))
```

注意对`.replace(‘\n’, ‘ \n ‘`的调用，这是因为我们希望换行符是一个单词。这背后的想法是，我们也让网络决定何时开始一个新的行(在几个字之后)。在这之后， *text_in_words* 是一个包含所有语料库的大数组，逐字逐句。

```
>>> text_in_words[3000:3005][‘ella’, ‘era’, ‘como’, ‘estar’, ‘\n’]
```

# 获取词频

在字符级文本生成器中，你可能有 30-50 个不同的*尺寸，*每个尺寸对应一个不同的字符。在像当前这样的词级生成器中，每个不同的词都有一个维度，这个维度可能有数万个(特别是在像这样肮脏的语料库中)。

为了避免这种大数量，我们计算每个单词的频率，因此我们可以使用这些信息来过滤不常用的单词，从而降低维数，从而减少训练网络的内存和时间。

```
*# Calculate word frequency* word_freq = {}
for word in text_in_words:
    word_freq[word] = word_freq.get(word, 0) + 1

ignored_words = set()
for k, v in word_freq.items():
    if word_freq[k] < MIN_WORD_FREQUENCY:
        ignored_words.add(k)

words = set(text_in_words)
print('Unique words before ignoring:', len(words))
print('Ignoring words with frequency <', MIN_WORD_FREQUENCY)
words = sorted(set(words) - ignored_words)
print('Unique words after ignoring:', len(words))

word_indices = dict((c, i) for i, c in enumerate(words))
indices_word = dict((i, c) for i, c in enumerate(words))
```

变量 *MIN_WORD_FREQUENCY* 是一个参数，它指示一个单词为了“入选”并出现在最终的单词词典中所需的最小出现次数。

换句话说，如果我们将*的最小词频*设置为 10，我们将只考虑在语料库中出现 10 次或更多次的单词。出于调试目的，我们打印原始字典的大小，以及删除不常用单词后的大小。

然后我们创建字典来从单词到索引以及从索引到单词进行翻译。这就像[的 keras-team 的例子。](https://github.com/keras-team/keras/blob/master/examples/lstm_text_generation.py)

# 创建和过滤序列

如果我们还记得，此时我们有了 *text_in_words* ，这是一个包含所有逐字语料库的数组。我们需要创建大小为 *SEQUENCE_LEN* 的序列(另一个可以手动选取的参数)并存储在*句子*中，在同一个索引中，存储 *next_words 中的下一个单词。*

但是有一个问题:在 *text_in_words* 中我们仍然有许多单词被忽略。我们不能直接删除这些词，因为这样会破坏语言，留下不连贯的句子。这就是为什么我们需要验证每个可能的序列+next_word，如果包含至少一个被忽略的单词，它应该被忽略。

```
*# cut the text in semi-redundant sequences of SEQUENCE_LEN words* STEP = 1
sentences = []
next_words = []
ignored = 0
for i in range(0, len(text_in_words) - SEQUENCE_LEN, STEP):
    *# Only add sequences where no word is in ignored_words* if len(set(text_in_words[i: i+SEQUENCE_LEN+1]).intersection(ignored_words)) == 0:
        sentences.append(text_in_words[i: i + SEQUENCE_LEN])
        next_words.append(text_in_words[i + SEQUENCE_LEN])
    else:
        ignored = ignored+1
print('Ignored sequences:', ignored)
print('Remaining sequences:', len(sentences))
```

# 洗牌和分割训练集

下一步是标准的，我们改组训练集，并把它分成训练集和测试集(默认为 98%-2%)。

```
sentences, next_words, sentences_test, next_words_test = shuffle_and_split_training_set(sentences, next_words)
```

# 构建模型

现在我们建立 RNN 模型。在这个例子中，我使用了一组两级堆叠的 LSTM 双向单元。

```
model = Sequential()
model.add(Bidirectional(LSTM(128), input_shape=(SEQUENCE_LEN, len(words))))
if dropout > 0:
    model.add(Dropout(dropout))
model.add(Dense(len(words)))
model.add(Activation('softmax'))
```

在这个架构中有几个任意的决定，我实际上没有时间交叉验证不同大小、不同类型的单元等。

这是一些关于架构可能性的讨论:

*   双向与常规 LSTM [在这里。](https://stackoverflow.com/questions/43035827/whats-the-difference-between-a-bidirectional-lstm-and-an-lstm)
*   辍学是一种正则化技术，以防止过度拟合[在这里](https://machinelearningmastery.com/dropout-regularization-deep-learning-models-keras/)。
*   LSTM 单位的数量，我怀疑 256 可能太多了。[讨论到这里。](https://datascience.stackexchange.com/questions/16350/how-many-lstm-cells-should-i-use/18049)

根据布尔参数 SIMPLE_MODEL，我们创建一层或两层模型。一层普通的 LSTM 应该足以给出相当好的结果，就像 keras 团队的例子一样。

```
model.add(LSTM(128, input_shape=(maxlen, len(chars))))
```

# 数据生成程序

没有想太多，在我的第一次尝试中，我想使用与角色级示例相同的 [model.fit](https://keras.io/models/sequential/) 策略，即一次将整个训练集发送给模型。这很容易也很快让我想到了**的内存不足错误**。

经过快速分析，我找到了原因。为了将序列矢量化为训练集(x，y ),我们需要这些 numpy 数组:

```
x = np.zeros((len(sentences), SEQUENCE_LEN, len(words)), dtype=np.bool)
y = np.zeros((len(sentences), len(words)), dtype=np.bool)
```

如果没有单词过滤，我大概有 100 万个句子(len(句子)= 1000000)，SEQUENCE_LEN = 10 和 40000 个不同的单词(LEN(单词)=40000)。使用这些数字， *x* 的大小为 400，000，000，000(！).考虑到 numpy 中的[是 1 字节](https://stackoverflow.com/questions/5602155/numpy-boolean-array-with-1-bit-entries)，这给了我大约 400 GB 的内存(！).

因此需要数据生成器。使用数据生成器，你用训练集的*块*来填充模型，每一步一个，而不是一次填充所有东西。

```
def generator(sentence_list, next_word_list, batch_size):
    index = 0
    while True:
        x = np.zeros((batch_size, SEQUENCE_LEN, len(words)), dtype=np.bool)
        y = np.zeros((batch_size, len(words)), dtype=np.bool)
        for i in range(batch_size):
            for t, w in enumerate(sentence_list[index]):
                x[i, t, word_indices[w]] = 1
            y[i, word_indices[next_word_list[index]]] = 1

            index = index + 1
            if index == len(sentence_list):
                index = 0
        yield x, y
```

生成器函数获取句子和 next_words 的列表，以及批处理的大小。然后它[产生](https://stackoverflow.com/questions/231767/what-does-the-yield-keyword-do)两个 *batch_size 的 numpy 数组。*我们使用*索引*变量来跟踪我们已经返回的例子。当然，当我们到达列表的末尾时，它需要重新初始化为 0。这个生成器既可以用于训练，也可以用于评估(只需通过不同的*句子 _ 列表*和*下一个 _ 单词 _ 列表*)。

# 完成模型

函数**样本**和 **on_epoch_end** 与 keras-team 示例中的[基本相同。然而，在模型编译中，我添加了几个 Keras](https://github.com/keras-team/keras/blob/master/examples/lstm_text_generation.py) [回调](https://keras.io/callbacks/)。

```
file_path = "./checkpoints/LSTM_LYRICS-epoch{epoch:03d}-words%d-sequence%d-minfreq%d-loss{loss:.4f}-acc{acc:.4f}-val_loss{val_loss:.4f}-val_acc{val_acc:.4f}"% (
    len(words),
    SEQUENCE_LEN,
    MIN_WORD_FREQUENCY
)
checkpoint = ModelCheckpoint(file_path, monitor='val_acc', save_best_only=True)
print_callback = LambdaCallback(on_epoch_end=on_epoch_end)
early_stopping = EarlyStopping(monitor='val_acc', patience=5)
callbacks_list = [checkpoint, print_callback, early_stopping]
```

第一个是**模型检查点**以保存每个时期的权重，第二个是**提前停止**以停止训练，如果在 5 个时期内损失没有增加。

# 训练模型

最后，我们用数据生成器、回调和时期数调用 model.fit_generator(而不是 model.fit)。我们还发送了另一个带有测试数据的生成器，因此它在每个时期都会得到评估。

```
model.fit_generator(generator(sentences, next_words, BATCH_SIZE),
    steps_per_epoch=int(len(sentences)/BATCH_SIZE) + 1,
    epochs=100,
    callbacks=callbacks_list,
    validation_data=generator(sentences_test, next_words_test, BATCH_SIZE),              validation_steps=int(len(sentences_test)/BATCH_SIZE) + 1)
```

rnn 可能很难训练。即使使用相当强大的 GPU (GeForce GTX 1070 ti ),使用堆叠的 LSTM 架构，每个纪元也需要一个多小时。

# 执行培训

开始训练，需要跑(自然可以用自己的语料库跑)。

```
git clone [https://github.com/enriqueav/lstm_lyrics.git](https://github.com/enriqueav/lstm_lyrics.git)
cd lstm_lyrics
python3 lstm_train.py corpora/corpus_banda.txt examples.txt
```

**更新:**也可以参考[这个词嵌入版本](/@monocasero/update-automatic-song-lyrics-creator-with-word-embeddings-e30de94db8d1)。

之后，脚本将打印关于当前训练集、预处理等的信息。示例语料库(墨西哥“banda”音乐)包含超过一百万个单词中的超过五百万个字符。

```
Corpus length in characters: 5502159Corpus length in words: 1066242
```

最初，语料库中有超过 35，000 个不同的单词。过滤掉频率小于 10 (MIN_WORD_FREQUENCY = 10)的词后，只有 6605 个。

```
Unique words before ignoring: 36990Ignoring words with frequency < 10Unique words after ignoring: 6605
```

由于步长为 1，最初大约有一百万个不同的序列。然而，由于我们已经忽略了 30，000 个不常用的单词，我们还需要忽略包含至少一个这些被忽略的单词的序列。经过这一切割，我们得到大约 537，000 个有效序列。

```
Ignored sequences: 529230Remaining sequences: 537002Shuffling sentencesShuffling finished
```

最后，我们在 98%训练 2%测试集中拆分这些混洗的 537，000。

```
Size of training set = 526261Size of test set = 10741
```

然后我们建立模型并开始训练

```
Build model...Epoch 1/100...
```

# 监控结果

就像字符级生成器一样，每个时期几个例句将被写入*示例*文本文件，种子是从原始语料库中随机选取的。

到第 20 个纪元时，训练集的准确率将在 90%左右，但是在测试集上，我们不会看到这么高的数字，这是正常的。请记住，我们的目标不是获得人类水平的准确性，而是学习“风格”并生成一些连贯的歌词。

# 例子

不幸的是，如果你懂一些西班牙语，特别是如果你熟悉墨西哥班达风格，这将更有意义。

> **生成种子:“米德 porque estoy compometido la que se que da la quiero y la que se”**
> 
> 米德说，他是一个勇敢的人，也是一个与世界和平共处的人，米德是一个热爱自己的人，因为他是黑手党的头目，他把我带到了世界和平的地方
> 
> **用种子生成:“你是哈特最后一个孩子吗？”**
> 
> 上次去哈特的时候，你们一起看了一会儿电影，看了一会儿电影，看了一会儿电影，有一个女人给我打电话，说她不喜欢我，但是她没有给我打电话
> 
> **用种子生成:" mis brazos me muero de ganas por volvert a besar en mis noches despierto gritando "**
> 
> 我的母亲是一个很大的孩子，因为我知道你的名字，我的母亲是另一个生活在美丽的地方的男人，因为我的母亲是一个很年轻的女人，所以她没有去过任何一个地方

# 后续步骤

目前，我只包括项目的培训部分。我将对此进行扩展(或创建另一个故事),以便实际使用训练模型从种子生成歌词。我还会上传已经训练好的网络的权重。

**2018 年 6 月 15 日更新:**更改为包括使用新行作为单独的单词，以及在 fit_generator 上发送验证数据。

**2019 . 1 . 21 更新:**添加链接到[故事的第二部分](/@monocasero/update-automatic-song-lyrics-creator-with-word-embeddings-e30de94db8d1)。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [杠杆代币的终极指南](/coinmonks/leveraged-token-3f5257808b22)
*   [加密交易的最佳 VPNs】](https://coincodecap.com/best-vpns-for-crypto-trading)
*   [最佳加密分析或链上数据](https://coincodecap.com/blockchain-analytics) | [Bexplus 评论](https://coincodecap.com/bexplus-review)
*   [NFT 十大市场造币集锦](https://coincodecap.com/nft-marketplaces)
*   [AscendEx Staking](https://coincodecap.com/ascendex-staking)|[Bot Ocean Review](https://coincodecap.com/bot-ocean-review)|[最佳比特币钱包](https://coincodecap.com/bitcoin-wallets-india)
*   [Bitget 回顾](https://coincodecap.com/bitget-review)|[Gemini vs block fi](https://coincodecap.com/gemini-vs-blockfi)|[OKEx 期货交易](https://coincodecap.com/okex-futures-trading)