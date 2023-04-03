# 从 Coinmarketcap 下载历史数据

> 原文：<https://medium.com/coinmonks/downloading-historical-data-from-coinmarketcap-41a2b0111baf?source=collection_archive---------1----------------------->

![](img/c082fbe46a7f94b07f89ddd1472e50c8.png)

Coinmarketcap 是价格和数据加密的绝佳资源。尽管[有缺陷](https://www.reddit.com/r/Bitcoin/comments/691za4/serious_flaws_with_coinmarketcapcom_more_reasons/)，但它仍然是一个非常受欢迎的查询 cryto 价格的网站，我发现自己出于习惯每天都会回到那里，尽管有其他令人信服的、可以说是更好的选择，如 [onchainfx](https://onchainfx.com) 。习惯需要努力去改变，coinmarketcap 是包括我在内的许多人的习惯，只要这个网站保持“足够好”，这个习惯就很难打破。

我最近发现自己想要下载 cryto 的历史数据，以便进行一些数据分析。尽管他们有一个公共 API，但似乎没有下载任何历史数据的功能。还有一个通知，这个 API 将于 2018 年 12 月下线，由一个[专业 API](https://pro.coinmarketcap.com) 替代。专业 API 将允许下载历史数据，但访问 12 个月的历史数据需要每月 699 美元的高昂价格，并且访问长达 5 年的历史数据需要“询价”。

这似乎有点陡峭，我发现自己有点震惊，因为加密是所有关于消除或减少中间人和寻租活动。像价格数据这样基本的东西当然应该是免费的。传统金融系统经常对价格数据收取疯狂的费用，加密生态系统肯定可以做得更好。

我决定构建一个脚本来自己下载数据，但幸运的是，我决定先看看管理网站的使用条款。实话实说，谁会这么做。就在那里。你不允许从网站上抓取或复制任何数据。对我来说，这似乎又是非常反加密的。想象一下一个公开的区块链有这样的条款。

```
(extract from the [Terms of Use](https://coinmarketcap.com/terms/))**Prohibited Activities**You agree that you will not:Use or introduce to the Service any data mining, crawling, “scraping”, robot or similar automated or data gathering or extraction method, or manually access, acquire, monitor or copy any portion of the Service, or download or store Content (unless expressly authorized by CMC). Certain data and other information within the Service is available by subscription, or for a fee, at [https://pro.coinmarketcap.com](https://pro.coinmarketcap.com/);
```

鉴于上述情况，这篇文章现在纯粹是假设性的。如果允许，我将如何从 coinmarket cap 下载数据。严格地说，这是我在 Python 中的做法。

首先，我会导入 BeautifulSoup 和其他一些方便的包。

```
from bs4 import BeautifulSoup
import requests
import pandas as pd
```

然后我会获取相关的页面并找到包含所有数据的表

```
url = "[https://coinmarketcap.com/currencies/ripple/historical-data/?start=20130428&end=20180802](https://coinmarketcap.com/currencies/ripple/historical-data/?start=20130428&end=20180802)"
content = requests.get(url).content
soup = BeautifulSoup(content,'html.parser')
table = soup.find('table', {'class': 'table'})
```

然后使用嵌套列表理解来遍历表格行和单元格，用数据建立一个列表列表。

```
data = [[td.text.strip() for td in tr.findChildren('td')] 
        for tr in table.findChildren('tr')]
```

最后，将列表转换为数据帧，并做一些清理工作，比如将文本日期转换为真实日期、删除空行、命名列、设置索引和排序。

```
df = pd.DataFrame(data)
df.drop(df.index[0], inplace=True) # first row is empty
df[0] =  pd.to_datetime(df[0]) # date
for i in range(1,7):
    df[i] = pd.to_numeric(df[i].str.replace(",","").str.replace("-","")) # some vol is missing and has -
df.columns = ['Date','Open','High','Low','Close','Volume','Market Cap']
df.set_index('Date',inplace=True)
df.sort_index(inplace=True)
```

仅此而已。Python 确实让生活充满乐趣。现在如果我们被允许这样做就好了。如果你知道一个很好的替代获取历史数据的方法，请在回复中发表一些东西。Quandl 有一些数据，但没有很多硬币的数据，他们的一些数据有错误(例如，BCH 分叉后的 coinbase 数据变得一塌糊涂)。

如果你喜欢这篇文章或者得到了什么好处，请在下面鼓掌，这样其他人也能发现它。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   杠杆代币的终极指南
*   [AscendEx Staking](https://coincodecap.com/ascendex-staking)|[Bot Ocean Review](https://coincodecap.com/bot-ocean-review)|[最佳比特币钱包](https://coincodecap.com/bitcoin-wallets-india)
*   [Bitget 回顾](https://coincodecap.com/bitget-review) | [双子 vs 区块链](https://coincodecap.com/gemini-vs-blockfi) | [OKEx 期货交易](https://coincodecap.com/okex-futures-trading)
*   [美国最佳加密交易机器人](https://coincodecap.com/crypto-trading-bots-in-the-us) | [经常性回顾](https://coincodecap.com/changelly-review)
*   [在印度利用加密套利赚取被动收入](https://coincodecap.com/crypto-arbitrage-in-india)
*   [霍比审核](https://coincodecap.com/huobi-review) | [OKEx 保证金交易](https://coincodecap.com/okex-margin-trading) | [期货交易](https://coincodecap.com/futures-trading)
*   [麻雀交换评论](https://coincodecap.com/sparrow-exchange-review) | [纳什交换评论](https://coincodecap.com/nash-exchange-review)