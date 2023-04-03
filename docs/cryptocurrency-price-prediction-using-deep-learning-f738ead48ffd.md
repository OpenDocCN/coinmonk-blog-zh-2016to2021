# 使用深度学习的加密货币价格预测

> 原文：<https://medium.com/coinmonks/cryptocurrency-price-prediction-using-deep-learning-f738ead48ffd?source=collection_archive---------0----------------------->

一个完整的机器学习现实世界的应用程序走查使用 LSTM 神经网络。

由于其市值连续几个月呈指数增长，加密货币的受欢迎程度在 2017 年飙升。价格在 2018 年 1 月达到峰值，超过 8000 亿美元。

尽管机器学习已经成功地通过大量不同的时间序列模型预测了股票市场价格，但它在预测加密货币价格方面的应用却受到了很大的限制。这背后的原因很明显，因为加密货币的价格取决于许多因素，如技术进步、内部竞争、市场交付压力、经济问题、安全问题、政治因素等。如果采取明智的发明策略，它们的高波动性导致高利润的巨大潜力。不幸的是，由于缺乏指数，与股票市场预测等传统金融预测相比，加密货币相对不可预测。

在这篇博客中，我将通过四个步骤来预测加密货币的价格:

1.  获取实时加密货币数据。
2.  为培训和测试准备数据。
3.  用 LSTM 神经网络预测加密货币的价格。
4.  将预测结果可视化。

# 挑战

使用数据集中的所有交易特征(如价格、交易量、开盘价、盘高、盘低值)来预测加密货币的价格。

# 数据

数据集可以从 CryptoCompare 网站下载，该网站可以在[这里](https://min-api.cryptocompare.com/)找到。

数据集总共包含 5 个要素。它们的详细信息如下:

1.  收盘价——这是当天货币的市场收盘价。
2.  高价——这是当天货币的最高价格。
3.  低价——这是当天货币的最低价格。
4.  开盘价—这是当天货币的市场开盘价。
5.  成交量——当天交易的货币量

# 代码在哪里？

事不宜迟，让我们从代码开始吧。GitHub 上的完整项目可以在[这里](https://github.com/subramanya1997/cryptocurrency-price-prediction)找到。

我从加载所有需要的库和依赖项开始。

```
import json
import requests
from keras.models import Sequential
from keras.layers import Activation, Dense, Dropout, LSTM
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import seaborn as sns
from sklearn.metrics import mean_absolute_error
%matplotlib inline
```

我使用了美元汇率，并将实时数据存储到 pandas 数据框架中。我用 to_datetime()方法将字符串日期时间转换成 Python 日期时间对象。这是必要的，因为文件中的日期时间对象被读取为字符串对象。对字符串而不是日期时间对象执行像时差这样的操作要容易得多。

```
endpoint = 'https://min-api.cryptocompare.com/data/histoday'
res = requests.get(endpoint + '?fsym=BTC&tsym=USD&limit=2000')
hist = pd.DataFrame(json.loads(res.content)['Data'])
hist = hist.set_index('time')
hist.index = pd.to_datetime(hist.index, unit='s')
target_col = 'close'
```

让我们看看数据集是怎样的，包括所有的交易特征，如价格、交易量、开盘价、最高价、最低价。

```
hist
```

![](img/bf7c52eccd5c9c249d4e2aac1a60b971.png)

接下来，我将数据分成两组—分别包含 80%和 20%数据的训练集和测试集。这里所做的决定只是为了本教程的目的。在真实的项目中，你应该总是把你的数据分成训练、验证、测试(比如 60%、20%、20%)。

```
def train_test_split(df, test_size=0.2):
    split_row = len(df) - int(test_size * len(df))
    train_data = df.iloc[:split_row]
    test_data = df.iloc[split_row:]
    return train_data, test_datatrain, test = train_test_split(hist, test_size=0.2)
```

现在，让我们使用下面的代码来绘制以美元为单位的加密货币价格与时间的函数关系图:

```
def line_plot(line1, line2, label1=None, label2=None, title='', lw=2):
    fig, ax = plt.subplots(1, figsize=(13, 7))
    ax.plot(line1, label=label1, linewidth=lw)
    ax.plot(line2, label=label2, linewidth=lw)
    ax.set_ylabel('price [CAD]', fontsize=14)
    ax.set_title(title, fontsize=16)
    ax.legend(loc='best', fontsize=16)line_plot(train[target_col], test[target_col], 'training', 'test', title='')
```

![](img/0feb288b881512ba29d1d1f1e862f0f8.png)

我们可以观察到，在 2018 年 12 月至 2019 年 4 月之间，价格出现了明显的下降。从 2019 年 4 月到 2019 年 8 月，价格持续上涨，7 月和 8 月出现波动。从 2019 年 9 月开始，价格不断下降。从这一价格波动中值得注意的有趣的事情是，价格在冬天很低，而在夏天上涨。虽然这不能一概而论，因为所考虑的数据集只是一年的小样本。同样，对于加密货币，很难一概而论。

接下来，我创建了几个函数来规范化这些值。标准化是一种经常作为机器学习的数据准备的一部分而应用的技术。规范化的目标是将数据集中数值列的值更改为一个通用的比例，而不会扭曲值范围的差异。

```
def normalise_zero_base(df):
    return df / df.iloc[0] - 1def normalise_min_max(df):
    return (df - df.min()) / (data.max() - df.min())
```

接下来，我创建了一个函数来提取大小为 5 的窗口的数据，如下面的代码所示:

```
def extract_window_data(df, window_len=5, zero_base=True):
    window_data = []
    for idx in range(len(df) - window_len):
        tmp = df[idx: (idx + window_len)].copy()
        if zero_base:
            tmp = normalise_zero_base(tmp)
        window_data.append(tmp.values)

    return np.array(window_data)
```

我继续创建一个函数，以某种格式准备数据，以便以后输入神经网络。我使用了同样的概念，将数据分成两组—分别包含 80%和 20%数据的训练集和测试集，如下面的代码所示:

```
def prepare_data(df, target_col, window_len=10, zero_base=True, test_size=0.2):
    train_data, test_data = train_test_split(df, test_size=test_size)
    X_train = extract_window_data(train_data, window_len, zero_base)
    X_test = extract_window_data(test_data, window_len, zero_base)
    y_train = train_data[target_col][window_len:].values
    y_test = test_data[target_col][window_len:].values
    if zero_base:
        y_train = y_train / train_data[target_col][:-window_len].values - 1
        y_test = y_test / test_data[target_col][:-window_len].values - 1 return train_data, test_data, X_train, X_test, y_train, y_test
```

# LSTM

它通过使用特殊的门来允许每个 LSTM 层从先前层和当前层获取信息。数据通过多个门(如遗忘门、输入门等。)和各种激活功能(如 tanh 功能、relu 功能)并通过 LSTM 细胞。这样做的主要优点是，它允许每个 LSTM 细胞在一定时间内记住模式。需要注意的是，LSTM 可以记住重要的信息，同时忘记不相关的信息。LSTM 架构如下所示:

![](img/c125a9412120a0dad80460fadc2b8868.png)

现在让我们建立模型。顺序模型用于堆叠所有层(输入、隐藏和输出)。神经网络包括 LSTM 层，其后是 20%的下降层和具有线性激活函数的密集层。我遵照模型，使用 Adam 作为优化器，使用均方误差作为损失函数。

```
def build_lstm_model(input_data, output_size, neurons=100, activ_func='linear', dropout=0.2, loss='mse', optimizer='adam'):
    model = Sequential()
    model.add(LSTM(neurons, input_shape =(input_data.shape[1],input_data.shape[2])))
    model.add(Dropout(dropout))
    model.add(Dense(units=output_size))
    model.add(Activation(activ_func))
    model.compile(loss=loss, optimizer=optimizer)

    return model
```

接下来，我设置了一些稍后要使用的参数。这些参数是——随机数种子、窗口长度、测试集大小、LSTM 层中的神经元数量、时期、批量大小、丢失、漏失和优化器。

```
np.random.seed(42)
window_len = 5
test_size = 0.2
zero_base = True
lstm_neurons = 100
epochs = 20
batch_size = 32
loss = 'mse'
dropout = 0.2
optimizer = 'adam'
```

现在，让我们使用输入 x_train 和标签 y_train 来训练模型。

```
train, test, X_train, X_test, y_train, y_test = prepare_data(hist, target_col, window_len=window_len, zero_base=zero_base, test_size=test_size)
model = build_lstm_model(X_train, output_size=1, neurons=lstm_neurons, dropout=dropout, loss=loss,optimizer=optimizer)
history = model.fit(X_train, y_train, epochs=epochs, batch_size=batch_size, verbose=1, shuffle=True)
```

我使用平均绝对误差(MAE)作为评估标准。选择 MAE 而不是均方根误差(RMSE)的原因是 MAE 更容易解释。RMSE 没有单独描述平均误差，因此更难理解。因为我们希望这个模型即使对非技术观众也能容易地解释，所以 MAE 看起来是一个更好的选择。

# 绝对平均误差

它测量一组预测中误差的平均大小，而不考虑它们的方向。它是实际观测值和预测观测值之间的绝对差异在测试样本上的平均值，其中所有个体差异都具有相同的权重。

MAE 值 0.029311972877135727 看起来不错。最后，让我们使用下面的代码绘制实际价格和预测价格:

```
preds = test[target_col].values[:-window_len] * (preds + 1)line_plot(targets, preds, 'actual', 'prediction', lw=3)
preds = pd.Series(index=targets.index, data=preds)
```

![](img/20e35050721c38a2f9af4c1716cc4c52.png)

# 结论

在本文中，我演示了如何使用 LSTM 神经网络实时预测加密货币的价格。我经历了四个步骤:获取实时加密货币数据、准备用于训练和测试的数据、使用 LSTM 神经网络预测价格，以及可视化预测结果。随意使用超参数或尝试不同的神经网络架构以获得更好的结果。

## 另外，阅读

*   最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   [莱杰 vs 特雷佐](/coinmonks/ledger-vs-trezor-best-hardware-wallet-to-secure-cryptocurrency-22c7a3fd391e)
*   了解比特币最好的[书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)
*   [3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92)
*   [AAX 交易所评论](/coinmonks/aax-exchange-review-2021-67c5ea09330c) |推荐代码、交易费用、利弊
*   [德里比特评论](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   [FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   [n 零审核](/coinmonks/ngrave-zero-review-c465cf8307fc)
*   [Bybit 交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)
*   [3Commas vs Cryptohopper](/coinmonks/cryptohopper-vs-3commas-vs-shrimpy-a2c16095b8fe)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   最佳 [monero 钱包](https://blog.coincodecap.com/best-monero-wallets)
*   [莱杰 nano s vs x](https://blog.coincodecap.com/ledger-nano-s-vs-x)
*   [bits gap vs 3 commas vs quad ency](https://blog.coincodecap.com/bitsgap-3commas-quadency)
*   [莱杰纳米 S vs 特雷佐 one vs 特雷佐 T vs 莱杰纳米 X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   Bitsgap 评论——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   [埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)
*   [SecuX Stone 评论](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)
*   [BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |从您的密码中赚取高达 8.6%的利息