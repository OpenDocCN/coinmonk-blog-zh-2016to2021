# 在 Haskell 中创建比特币交易所

> 原文：<https://medium.com/coinmonks/roll-your-own-bitcoin-exchange-in-haskell-a8d782ab2a24?source=collection_archive---------8----------------------->

## 在 Haskell 推出自己的比特币交易所

![](img/5f4f60483addac0e8ba4c7cabba19644.png)

[Source](https://www.abcfinanze.com/investimenti/trading-bitcoin/)

> **归属** —这是[迈克尔·伯格的](https://twitter.com/TheMichaelBurge)博文
> [在哈斯克尔](https://www.michaelburge.us/2017/08/31/roll-your-own-bitcoin-exchange.html)推出你自己的比特币交易所的转载作品

[![](img/24325228c537a09e28d6e4d8a7d100b8.png)](https://coinmonks.com)

**Click to Find blockchain Jobs**

[**使用 Coinmonks 作业门户**](https://coinmonks.com/) 查找加密作业

`[**Get published on Coinmonks**](https://medium.com/coinmonks/contribute/home)`

[***捐造僧***](/coinmonks/monks-need-your-help-7440418d67ec)

证券交易所是一个复杂的庞然大物，但它的大部分可以简化为一个单一的数据结构:订单簿。比特币交易所在交易美元、BTC 或瑞士法郎的货币对时使用相同的数据结构。本文将向您展示如何:

*   设计一个可以处理限价订单和市价订单的订单簿
*   安装自动健全性检查，在每次写入订单时运行，防止黑客攻击和实施错误
*   构建一个人们可以用来与订单簿交互的 HTTP API

我们不会使用实际的比特币或钱包，因为它们增加了很多复杂性和风险，而不会让文章变得更有趣。相反，我们将假设“计费代码”已经写好，并且只关注交换的订单簿部分。

# 类型

那么什么是订单簿呢？

首先，我们将定义订单:

```
**import** Data.Tagged
**import** **qualified** Data.Map **as** M
**import** **qualified** Data.Sequence **as** Q

**type** **UserId** **=** **Integer**

**type** **Currency** **=** **Text**
**type** **CurrencyPair** **=** (**Currency**, **Currency**)
**type** **Amount** **=** **Integer**
**type** **Price** **=** **Double**

**data** **LimitOrder** **=** **LimitOrder** {
  _lorder_user         **::** **UserId**,
  _lorder_fromAmount   **::** **Amount**,
  _lorder_toAmount     **::** **Amount**
  } **deriving** (**Eq**, **Show**, **Generic**)

**data** **TBid**
**data** **TAsk**

**type** **BidT** a **=** **Tagged** **TBid** a
**type** **AskT** a **=** **Tagged** **TAsk** a
**type** **Bid** **=** **BidT** **LimitOrder**
**type** **Ask** **=** **AskT** **LimitOrder**

**data** **MarketOrder** **=** **MarketOrder** {
  _morder_user   **::** **UserId**,
  _morder_amount **::** **Amount**
  } **deriving** (**Eq**, **Show**, **Generic**)

**type** **MBid** **=** **BidT** **MarketOrder**
**type** **MAsk** **=** **AskT** **MarketOrder**

**data** **OrderBookF** a **=** **OrderBook** {
  _book_fromCurrency **::** **Currency**,
  _book_toCurrency   **::** **Currency**,
  _book_bids         **::** **M.Map** **Price** (**Q.Seq** (**BidT** a)),
  _book_asks         **::** **M.Map** **Price** (**Q.Seq** (**AskT** a))
  } **deriving** (**Eq**, **Show**, **Functor**, **Traversable**, **Foldable**)
**type** **OrderBook** **=** **OrderBookF** **LimitOrder**
```

有几点需要注意:

*   我们使用`Seq`而不是`List`或`Vector`，因为我们需要相对高效的插入和搜索。
*   更高阶的`OrderBookF`让我们不用写任何代码就可以得到`Traversable`、`Functor`和`Foldable`实例来操作订单簿。
*   `Bid`和`Ask`都是`LimitOrder`的，但是我想在类型系统中单独跟踪它们。所以我使用`Tagged`来附加一个`TBid`或`TAsk`标签。

仅仅有`OrderBook`是不够的，因为我们想要跟踪买家和卖家之间实际转移的最终金额。让我们为此添加一些类型:

```
**data** **SingleEntry** **=** **SingleEntry** {
  _se_account  **::** **UserId**,
  _se_currency **::** **Currency**,
  _se_amount   **::** **Amount**
  } **deriving** (**Eq**, **Show**, **Generic**)

**data** **DoubleEntry** **=** **DoubleEntry** {
  _de_fromAccount **::** **UserId**,
  _de_toAccount   **::** **UserId**,
  _de_currency    **::** **Currency**,
  _de_amount      **::** **Amount**
  } **deriving** (**Eq**, **Show**, **Generic**)

**data** **TradeF** a **=** **Trade** {
  _trade_from **::** a,
  _trade_to   **::** a
  } **deriving** (**Eq**, **Show**, **Functor**, **Traversable**, **Foldable**, **Generic**)
**type** **Trade** **=** **TradeF** **DoubleEntry**

**data** **External**
**type** **ExternalTransfer** **=** **Tagged** **External** **SingleEntry**
```

“执行”一个订单会导致它变成至少一个`Trade`。每当美元/BTC 货币对出现`Trade`时，用户的余额会发生 4 种变化:

*   买家损失了他的美元
*   卖方获得美元
*   买方获得 BTC
*   卖家失去了 BTC

在处理任何种类的资金时，使用“复式记账法”是很重要的。每当钱易手时，在双方都增加一个分录，就能更好地防止记账错误。内部转账时，美元和 BTC 的总额应为零和。

有些账户在我们的交易所之外，比如银行账户中的美元，或者钱包中的 BTC。在本例中，我选择了从交易所的视图中删除外部帐户，这样对于交易所之外的转账，我们只有单一条目簿记。出于会计目的，应该有一个单独的数据库来保存完整的复式记账财务数据，并且应该定期与交易所、任何银行账户和任何钱包进行核对。

管理员或计费代码可以在创建或销毁交换中的货币供应时创建`SingleEntry`记录，而所有用户将在彼此之间创建`DoubleEntry`记录。

最后，这是交换将保持的全局状态:

```
**data** **Exchange** **=** **Exchange** {
  _exchange_external **::** **TVar** (**Q.Seq** **ExternalTransfer**),
  _exchange_book     **::** **TVar** **OrderBook**,
  _exchange_trades   **::** **TVar** (**Q.Seq** **Trade**)
  }
```

简而言之，一个`Exchange`是一个外部转移的集合，将资金移入和移出系统，是一个单一货币对的订单簿，装满了等待执行的订单，以及以前的交易历史。

这个交易所没有交易费。这些将会是`Trade`或`External`类型，这取决于我们是对内部还是外部转移收费。

# 定货簿

使用我们的订单簿需要哪些基本操作？我们希望:

*   在账簿中添加订单，如果可以的话，自动执行交易。
*   取消订单
*   在订单簿上列出所有未完成的订单

一旦我们有了这些，我们将把它包装在一个 HTTP API 中，脚本或网站可以使用它来与订单簿进行交互。

我现在只看`Bid`限价和市价单`Ask`版本非常相似。以下是我们需要的函数类型:

```
cancelBid **::** **Bid** **->** **OrderBook** **->** **OrderBook**
fillBid **::** **Bid** **->** **OrderBook** **->** ([**Trade**], **OrderBook**)
tryFillMBid **::** **MBid** **->** **Balances** **->** **OrderBook** **->** (**Maybe** **MBid**, [**Trade**], **OrderBook**)
listOrders **::** **OrderBook** **->** [**LimitOrder**]
listOrders **=** toList
```

因为生成的`Foldable`实例为我们做了`listOrders`，所以我继续并实现了它。我将把`cancelBid`的实现留在 [Github](https://github.com/MichaelBurge/lambda-exchange) 上。

对于`fillBid`和`tryFillMBid`，我们将为限价订单编写一个合适的通用函数，并假设`MarketOrder`是一个不能保存在订单簿中的`LimitOrder`，用户在这里对其整个账户余额进行竞价:

```
fillBid **::** **Bid** **->** **OrderBook** **->** ([**Trade**], **OrderBook**)
fillBid bid book **=** **case** matchBid bid book **of**
  (**Nothing**, trades, book) **->**
    (trades, book)
  (**Just** bidRemainder, trades, book) **->**
    (trades, unsafe_addBid bidRemainder book)

tryFillMBid **::** **MBid** **->** **Balances** **->** **OrderBook** **->** (**Maybe** **MBid**, [**Trade**], **OrderBook**)
tryFillMBid mbid bals book**@OrderBook**{**..**} **=**
  **case** **M.**lookup _book_toCurrency bals **of**
    **Nothing** **->** (**Nothing**, **[]**, book)
    **Just** toBalance **->**
      *-- A Market order is a non-persistable limit order with the user bidding his entire balance.*
      **let** (**Tagged** **MarketOrder**{**..**}) **=** mbid
          bid **=** **Tagged** **$** **LimitOrder** {
            _lorder_user **=** _morder_user,
            _lorder_fromAmount **=** _morder_amount,
            _lorder_toAmount **=** toBalance
            }
      **in** **case** matchBid bid book **of**
        (**Nothing**, trades, book) **->**
          (**Nothing**, trades, book)
        (**Just** bid, trades, book) **->**
          (**Just** **$** bidToMbid bid, trades, book)

bidToMBid **::** **Bid** **->** **MBid**
unsafe_addBid **::** **Ask** **->** **OrderBook** **->** **OrderBook**
```

我们的通用`matchBid`函数试图在`OrderBook`上填充一个限价单，并返回一个新的`Bid`，用于任何无法匹配的、任何已执行的`Trade`，以及新的`OrderBook`:

```
matchBid **::** **Bid** **->** **OrderBook** **->** (**Maybe** **Bid**, [**Trade**], **OrderBook**)
matchBid bid book **=**
  **let** pair **=** _book_pair book
      loop **::** (**Bid**, [**Trade**], **OrderBook**) **->** (**Maybe** **Bid**, [**Trade**], **OrderBook**)
      loop x**@**(bid, trades, book) **=**
        **case** lowestAsk book **of**
          *-- Case 1: The order book has no asks*
          (**Nothing**, **_**) **->** (**Just** bid, **[]**, book)
          (**Just** lowestAsk, deletedBook) **->**
            **case** mergeBid pair bid lowestAsk **of**
              *-- Case 2: The bid was unable to be matched*
              (**Just** bid, **Just** **_**, **Nothing**) **->** (**Just** bid, trades,book)
              *-- Case 3: The bid was partially matched; repeat the loop*
              (**Just** bidRemainder, **Nothing**, **Just** trade) **->**
                loop (bidRemainder, trade**:**trades, deletedBook)
              *-- Case 4: The ask was partially matched; terminate the loop.*
              (**Nothing**, **Just** askRemainder, **Just** trade) **->**
                (**Nothing**, trade**:**trades, unsafe_addAsk askRemainder deletedBook)
              *-- Case 5: The bid and ask exactly canceled each other out*
              (**Nothing**, **Nothing**, **Just** trade) **->**
                (**Nothing**, trade**:**trades, deletedBook)
              *-- Case 6: Impossible cases*
              x **->** panic **$** "fillBid: Unexpected case: " **<>** show x
  **in** loop (bid, **[]**, book)

mergeBid **::** **CurrencyPair** **->** **Bid** **->** **Ask** **->** (**Maybe** **Bid**, **Maybe** **Ask**, **Maybe** **Trade**)

lowestAsk **::** **OrderBook** **->** (**Maybe** **Ask**, **OrderBook**)

unsafe_addAsk **::** **Ask** **->** **OrderBook** **->** **OrderBook**
```

在这里，我们反复寻找订单簿上的最佳价格`Ask`，用它来填充我们的`Bid`，当我们用完合格的`Ask`时就停止。

`lowestAsk`很简单，因为我们的`Map`是按照`price`排序的。我将在 [Github](https://github.com/MichaelBurge/lambda-exchange) 库中定义它和`unsafe_addAsk`。

`mergeBid`可能是最复杂的，因为它处理 3 件不同的事情:

*   如果出价或要价被部分满足，它将生成新订单
*   如果买价和卖价交叉，就会产生交易
*   它处理决定这些的计算

```
mergeBid **::** **CurrencyPair** **->** **Bid** **->** **Ask** **->** (**Maybe** **Bid**, **Maybe** **Ask**, **Maybe** **Trade**)
mergeBid (fromCurrency, toCurrency) bid ask **=**
  **let** bidOrder **=** unTagged bid
      askOrder **=** unTagged ask
      n1 **=** _lorder_fromAmount bidOrder
      d1 **=** _lorder_toAmount bidOrder
      n2 **=** negate **$** _lorder_fromAmount askOrder
      d2 **=** _lorder_toAmount askOrder
      buyer **=** _lorder_user bidOrder
      seller **=** _lorder_user askOrder
      fi **=** fromIntegral
      *-- If seller rounds down, price would be below his limit.*
      sellerPrice **=** ceiling (fi n2 **/** fi d2)
      *-- If buyer rounds up, price would be above his limit.*
      buyerPrice **=** floor (fi n1 **/** fi d1)

      unitPrice **=** buyerPrice
      numUnits **=** min n1 n2
      toAmount **=** ceiling (fi numUnits **/** fi unitPrice)
      fromTransfer **=** **DoubleEntry** {
        _de_fromAccount **=** seller,
        _de_toAccount   **=** buyer,
        _de_amount      **=** numUnits,
        _de_currency    **=** fromCurrency
        }
      toTransfer **=** **DoubleEntry** {
        _de_fromAccount **=** buyer,
        _de_toAccount   **=** seller,
        _de_amount      **=** toAmount,
        _de_currency    **=** toCurrency
        }
      trade **=** **Trade** fromTransfer toTransfer
      (mNewBid, mNewAsk) **=** **case** d1 `compare` d2 **of**
        *-- Case 1: Buyer is done; seller still has inventory*
        **LT** **->** **let** newAsk **=** **Tagged** **$** **LimitOrder** {
                    _lorder_user       **=** seller,
                    _lorder_fromAmount **=** n2 **-** numUnits,
                    _lorder_toAmount   **=** sellerPrice
                    }
              **in** (**Nothing**, **Just** newAsk)
        *-- Case 2: Seller is out; buyer needs more*
        **GT** **->** **let** newBid **=** **Tagged** **$** **LimitOrder** {
                    _lorder_user       **=** buyer,
                    _lorder_fromAmount **=** n1 **-** numUnits,
                    _lorder_toAmount   **=** buyerPrice
                    }
              **in** (**Just** newBid, **Nothing**)
        *-- Case 3: Buyer and seller exactly traded*
        **EQ** **->** (**Nothing**, **Nothing**)
  **in** **if** buyerPrice **>=** sellerPrice
     *-- Bid has crossed the ask, so we can generate a trade.*
     **then** (mNewBid, mNewAsk, **Just** trade)
     *-- Bid is less than ask, so they can't be merged.*
     **else** (**Just** bid, **Just** ask, **Nothing**)
```

`mergeBid`非常微妙，以至于你可能不放心仅仅基于视觉检查来信任它的实现。在下一节中，我们将在每次写入时安装自动化的健全性检查，以便阻止任何实现错误。

# 安全性

当我们不可避免地被黑客攻击时，我们如何阻止我们的交易所亏损？

最重要的是确保我们交易所拥有的比特币或以太币钱包的安全。幸运的是，我们没有钱包，避免了这个问题。

第二件最重要的事情是拥有仅附加备份，如果我们检测到黑客攻击，我们可以回滚到该备份。这并不理想，因为我们仍然必须告诉我们所有的客户我们被黑了。

第三件最重要的事情是首先要避免损失这笔钱。

在我的上一篇文章中，我展示了如何使用定理证明器来正式证明关于你的数据结构的某些属性成立。我们不能在 Haskell 中做到这一点，但是让我为你定义几个不变量，我将向你展示一个技巧。

# 不变量

以下是我们的词汇不变量:

*   用户不能和自己交易
*   用户不能有负余额
*   用户在未决交易中的资金不能超过他们账户中的资金。
*   用户和订单簿不能有不存在的货币

这是他们的代码:

*   s 不能与自己交易

```
**type** **ConsistencyCheck** **=** **Exchange** **->** **STM** **Bool**

consistency_noSelfTrades **::** **ConsistencyCheck**
consistency_noSelfTrades **=** **\**exchange **->** **do**
  trades **<-** readTVar **$** _exchange_trades exchange
  return **$** all checkTrade trades
  **where**
    checkDe **DoubleEntry**{**..**} **=** _de_fromAccount **/=** _de_toAccount
    checkTrade **Trade**{**..**} **=** checkDe _trade_from **&&** checkDe _trade_to
```

*   用户不能有负余额

```
consistency_noNegativeBalances **::** **ConsistencyCheck**
consistency_noNegativeBalances **=** **\**exchange **->** **do**
  bals **<-** userBalances exchange
  **let** checkUser (userId, balances) **=**
        flip all (**M.**toList balances) **$** **\**(currency, balance) **->**
        **if** balance **>=** 0
        **then** **True**
        **else** **error** **$** "Negative balance for " **<>** show (userId, currency, balance)
  return **$** all checkUser **$** **M.**toList bals

userBalances **::** **Exchange** **->** **STM** (**M.Map** **UserId** **Balances**)
userBalances **=** undefined
```

*   用户在未决交易中的资金不能超过他们账户中的资金。

```
consistency_ordersBackedByAccount **::** **ConsistencyCheck**
consistency_ordersBackedByAccount **=** **\**exchange **->** **do**
  usersBals **<-** userBalances exchange

  **let** checkUserBalance **::** **Balances** **->** (**Currency**, **Amount**) **->** **Bool**
      checkUserBalance userBals (currency, bookAmount) **=**
        **case** **M.**lookup currency userBals **of**
          **Nothing** **->** **False**
          **Just** userAmount **->** userAmount **>=** bookAmount

  **let** checkUser **::** (**UserId**, **Balances**) **->** **STM** **Bool**
      checkUser (user, userBals) **=** **do**
        bookBals **<-** userBookBalances exchange user
        **let** currenciesPending **=** **M.**toList bookBals
        return **$** all (checkUserBalance userBals) currenciesPending
  allM checkUser **$** **M.**toList usersBals

userBookBalances **::** **Exchange** **->** **UserId** **->** **STM** **Balances**
userBookBalances **=** undefined
```

*   `User` s 和`OrderBook` s 不能有不存在的货币

```
consistency_allCurrenciesExist **::** **ConsistencyCheck**
consistency_allCurrenciesExist **=** **\**exchange **->** **do**
  usersBals **<-** userBalances exchange
  bookBals **<-** bookBalances exchange
  **let** valid currency **=** currency `elem` allCurrencies
      checkBals bals **=** all valid **$** **M.**keys bals
      usersCheck **=** all checkBals usersBals
      booksCheck **=** all valid **$** **M.**keys bookBals
  return **$** usersCheck **&&** booksCheck

bookBalances **::** **Exchange** **->** **STM** **Balances**
bookBalances **=** undefined
```

只要这些函数总是返回 true，我们就可以对剩下的代码有一些信心。

`userBalances`、`bookBalances`、`userBookBalances`汇总交易和对外转账，得到最终余额。我将把它们的实现放在 [Github](https://github.com/MichaelBurge/lambda-exchange) 库中。

# 诀窍是

人们经常在关系数据库中使用触发器或约束来自动执行不变量。使用 Haskell 的软件事务内存库，我们可以做一些类似的事情:

```
installSanityChecks **::** **Exchange** **->** **IO** ()
installSanityChecks exchange **=**
  atomically **$** mapM_ installCheck [
    (consistency_noNegativeBalances, "No negative balances"),
    (consistency_ordersBackedByAccount, "Orders must be backed by account"),
    (consistency_allCurrenciesExist, "Non-existent currency"),
    (consistency_noSelfTrades, "Users cannot trade with themselves")
    ]
  **where**
    installCheck (check, message) **=** alwaysSucceeds **$** **do**
      b **<-** check exchange
      **if** b
        **then** return ()
        **else** **error** message
```

`atomically`进入`STM`意义上的“交易”。它被设计成一种允许多个线程并发更新共享数据结构的有效方式。如果存在对同一变量的并发更新，事务可以中止并重试。如果我们的一个健全性检查失败，我们也可以中止。

`alwaysSucceed`函数将运行一次健全性检查，如果通过，则在以后的每个事务之后运行它。如果健全性检查因异常而失败，它将回滚有异常的事务。

在初始化或加载我们的交换状态之后，我们将在程序开始时调用`installSanityChecks`。那么每一次写操作都将被自动地进行完整性检查，并在出现异常的情况下回滚。我们的 HTTP 库`warp`将捕获异常并中止请求。

# 建立工作关系网

我们需要 5 个 API 端点:

*   列出订单
*   取消订单
*   添加订单
*   在交易所创造货币
*   列出余额

以下是请求类型:

```
**data** **Request_ListOrders** **=** **Request_ListOrders** {
  _reqListOrders_user **::** **Maybe** **UserId**
  } **deriving** (**Eq**, **Show**, **Generic**)

**data** **Request_CancelOrder** **=** **Request_CancelBid** {
  _reqCancelOrder_bid **::** **Bid**
  } **|** **Request_CancelAsk** {
  _reqCancelOrder_ask **::** **Ask**
  } **deriving** (**Eq**, **Show**, **Generic**)

**data** **Request_AddOrder** **=** **Request_AddBid** {
  _reqAddOrder_bid **::** **Bid**
  } **|** **Request_AddAsk** {
  _reqAddOrder_ask **::** **Ask**
  } **|** **Request_AddMBid** {
  _reqAddOrder_mbid **::** **MBid**
  } **|** **Request_AddMAsk** {
  _reqAddOrder_mask **::** **MAsk**
  } **deriving** (**Eq**, **Show**, **Generic**)

**data** **Request_CreateMoney** **=** **Request_CreateMoney** {
  _reqCreateMoney_singleEntry **::** **SingleEntry**
  } **deriving** (**Eq**, **Show**, **Generic**)

**data** **Request_ListBalances** **=** **Request_ListBalances** **deriving** (**Eq**, **Show**, **Generic**)
```

和响应类型:

```
**data** **Response_ListOrders** **=** **Response_ListOrders** {
  _resListOrders_orders **::** [**LimitOrder**]
  } **deriving** (**Eq**, **Show**, **Generic**)

**data** **Response_CancelOrder** **=** **Response_CancelOrder** **deriving** (**Eq**, **Show**, **Generic**)
**data** **Response_AddOrder** **=** **Response_AddBid** {
  _resAddOrder_trades **::** [**Trade**]  
  } **|** **Response_AddAsk** {
  _resAddOrder_trades **::** [**Trade**]
  } **|** **Response_AddMBid** {
  _resAddOrder_mbidRemainder **::** **Maybe** **MBid**,
  _resAddOrder_trades        **::** [**Trade**]
  } **|** **Response_AddMAsk** {
  _resAddOrder_maskRemainder **::** **Maybe** **MAsk**,
  _resAddOrder_trades        **::** [**Trade**]
  } **deriving** (**Eq**, **Show**, **Generic**)

**data** **Response_CreateMoney** **=** **Response_CreateMoney** **deriving** (**Eq**, **Show**, **Generic**)

**data** **Response_ListBalances** **=** **Response_ListBalances** {
  _resListBalances_externals **::** [(**UserId**, **Currency**, **Amount**)],
  _resListBalances_internals **::** [(**UserId**, **Currency**, **Amount**)],
  _resListBalances_helds     **::** [(**UserId**, **Currency**, **Amount**)],
  _resListBalances_totalBals **::** [(**UserId**, **Currency**, **Amount**)],
  _resListBalances_bookBals  **::** [(**Currency**, **Amount**)]
  } **deriving** (**Eq**, **Show**, **Generic**)
```

任何想要与我们的交易所互动的人最终都会创建一个`Request`并收到一个合适的`Response`。下面是服务器的实际入口点:

```
**import** Network.Wai **as** W
**import** Network.Wai.Handler.Warp
**import** Network.HTTP.Types.Method
**import** Network.HTTP.Types.Status

le_port **=** 2345

serverMain **::** **IO** ()
serverMain **=** **do**
  state **<-** initialize
  installSanityChecks state
  putStrLn **$** "Listening on " **++** show le_port
  run le_port **$** **\**req respond **->**
    **let** **?**req **=** req
        **?**respond **=** respond
        **?**state **=** state
    **in** **do**
      print req
      body **<-** strictRequestBody req
      **case** (pathInfo req, requestMethod req) **of**
        ("createMoney" **:** **_**, "POST") **->** withParsedRequest body api_createMoney
        ("listOrders" **:** **_**,  "POST") **->** withParsedRequest body api_listOrders
        ("addOrder" **:** **_**,    "POST") **->** withParsedRequest body api_addOrder
        ("cancelOrder" **:** **_**, "POST") **->** withParsedRequest body api_cancelOrder
        ("listBalances" **:** **_**, "POST") **->** withParsedRequest body api_listBalances
        **_** **->** respond (**W.**responseLBS status404 **[]** "Unknown path")

initialize **::** **IO** **Exchange**
initialize **=** **Exchange** **<$>** newTVarIO **Q.**empty **<*>** newTVarIO (newBook ("USD", "BTC")) **<*>** newTVarIO **Q.**empty

**type** **HandlerT** a **=** (**?**req **::** **Request**, **?**respond **::** (**Response** **->** **IO** **ResponseReceived**), **?**state **::** **Exchange**) **=>** a **->** **IO** **ResponseReceived**

withParsedRequest **::** **FromJSON** a **=>** **BSL.ByteString** **->** **HandlerT** (a **->** **IO** **ResponseReceived**)
withParsedRequest bs handler **=** **case** decode bs **of**
  **Nothing** **->** **?**respond (**W.**responseLBS status400 **[]** "Unable to parse")
  **Just** x **->** handler x
```

下面是如何实现我们的一个处理程序:

```
api_listOrders **::** **HandlerT** **Request_ListOrders**
api_listOrders **_** **=** **do**
  **let** **Exchange**{**..**} **=** **?**state
  book **<-** readTVarIO _exchange_book
  **?**respond **$** **W.**responseLBS status200 **[]** **$** encode **$** **Response_ListOrders** **$** toList book
```

我还在 [Github](https://github.com/MichaelBurge/lambda-exchange) 库中包含了 JSON 序列化器和反序列化器。

# 测试

我们实际上如何端到端测试我们的比特币交易所？

第一步是打印一个请求值，以获得其对应的 JSON:

```
print **$** encode **$** **Request_AddBid** **$** **Tagged** **$** **LimitOrder** {
    _lorder_user **=** 1
    _lorder_fromAmount **=** 4600,
    _lorder_toAmount **=** 1
    }
```

第二步是使用 shell 脚本将 JSON 发送到服务器:

```
cat **<<**'EOF' | curl localhost:2345/addOrder -XPOST -d @-
{
  "bid":{
    "toAmount":1,
    "user":1,
    "fromAmount":4600
  },
  "tag":"Request_AddBid"
}
EOF
```

此时，您可以使用 5 个端点的任意组合来更改或检查交换的状态。

# 结论

我们展示了如何在 Haskell 中实现一个简单的订单簿，它可以成为成熟的比特币交易所的基础。未来的文章可能包括:

*   为了提高效率，用 C 编写订单簿，并在更大的 Haskell 程序中使用。
*   编写一个程序，监视实际的比特币被发送，这样钱就可以进入我们的交易所。
*   使用单元测试来验证订单簿的实现。
*   向交易所添加多个货币对。
*   添加身份验证，这样用户就不能无限制地访问 exchange。