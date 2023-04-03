# 如何使用 Python 创建 Ichimoku 交易机器人(第 2 部分:构建机器人)

> 原文：<https://medium.com/coinmonks/how-to-create-an-ichimoku-trading-bot-using-python-part-2-building-the-bot-9662db31c5ff?source=collection_archive---------2----------------------->

![](img/07eba7a13a2e0bdb3ceee1e01e0e21f9.png)

Photo by [Clément Hélardot](https://unsplash.com/@clemhlrdt?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

你可以在这里阅读[***Part 1***](/coinmonks/how-to-create-an-ichimoku-trading-bot-on-python-part-1-the-strategy-b144f79960f8)。

现在让我们开始使用 python 构建 Ichimoku 交易机器人。

首先，我想将必要的信息存储到字典中。此外，我需要从 yahoo finance 获取大约 80 天的股票数据，只需将股票代码输入该函数即可(我使用的是 yahoo finance API btw)。因此，下面是定义的函数:

```
def stock(a,b,c,d,e,f):
    x = {"exchange":a,"ticker":b,"price":c,"atr":d,"sma_vol30":e,"current_vol":f}
    return xdef getinfo(x):
    symbol=x
    state=yf.Ticker(symbol).history(period="max")
    state = state[len(state)-80:len(state)]
    Data=state.loc[:,['Open','High','Low','Close']]
    Data=Data.iloc[::-1]
    return Data
```

接下来，我必须计算所有必要的值，我编写了一个函数，它接受来自交易所的所有报价机列表的输入(在这种情况下，报价机列表定义为 s)。然后我创建一个空的购买列表来存储所有满足条件的 tickers 字典。接下来，我将每个股票代码传递到循环 的 ***中，然后循环***计算必要的指标(转换、基数、span A、span B)，然后检查股票是否满足给定的要求(这是使用 ***if-else 语句*** 完成的)。如果股票符合 Ichimoku 策略中提出的所有指定条件，股票信息将作为字典存储到买入列表中。

随后我写了一个函数来决定我应该为前面的 ichimoku_analyser 函数挑选的股票购买多少股票。计算方法很简单，就是用分配给每只股票的金额除以股价。

```
def weightage(price,max_per_stock):
     weight=[math.floor(num)for num in list(max_per_stock/price)]
     return weight
```

现在我们已经完成了所有的功能，我们只需读取 tickers 列表并执行所有的功能。然后，我将所有相关信息整理成一个数据框架(包括买入日期和 20%止损)。数据框架是根据股票的相对交易量排列的，因此我可以很容易地对交易更活跃的股票进行优先排序。我还过滤了最近一天内涨幅较大的股票，因为这可能会产生错误信号。这是因为云是由大量一次性增加而不是上升趋势造成的。

然后，我将生成的数据帧附加到一个名为 portfolio.csv 的 csv 文件中

```
df.to_csv(‘portfolio.csv’,mode=’a’, index=False)
```

最后，我设计了一个函数来读取 portfolio.csv 文件，并识别那些有卖出信号或止损的股票。然后，我将所有这些待出售股票的相关信息放入另一个数据框中。

当更新 portfolio.csv 文件以删除要出售的股票时，具有要出售的股票的所有信息的数据帧(sell_df)被附加到 sell_list.csv。

```
df_port.to_csv(‘portfolio.csv’,mode=’w’, index=True)
sell_df.to_csv(‘sell_list.csv’,mode=’a’, index=False)
```

我们完了。现在，您已经创建了一个 ichimoku 云交易机器人，它根据我们之前的 ichimoku 策略中设定的条件来分析股票。

> 加入 [Coinmonks 电报频道](https://t.me/coincodecap)，了解加密交易和投资

## 另外，阅读

[](https://blog.coincodecap.com/crypto-exchange) [## 最佳加密交易所| 2021 年十大加密货币交易所

### 加密货币交易所的加密交易需要了解市场，这可以帮助你获得利润…

blog.coincodecap.com](https://blog.coincodecap.com/crypto-exchange)