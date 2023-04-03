# CCXT 加密货币交易-限价单和市价单[教程]

> 原文：<https://medium.com/coinmonks/ccxt-cryptocurrency-trading-limit-orders-and-market-orders-tutorial-26a5697fe076?source=collection_archive---------0----------------------->

![](img/f2665fcfb6178755ad1e412a4c3f0830.png)

对于像“加密货币交易所交易库”这样的名称，您可能想对 CCXT 库做的第一件事就是执行交易。

加密货币交易所在处理交易的方式、为开发人员提供的终端以及如何将这些终端集成到应用程序方面有很大不同。

> 这就是 CCXT 发挥作用的地方。

加密货币交易所的复杂性导致了对库的迫切需求，库可以统一开发人员与每个交易所的交互方式。

在本教程中，我们将探索流行的订单类型以及如何在交易所执行这些不同的订单。

> 加密货币中流行的交易指令类型有哪些？

有许多不同的订单类型，但本教程的主要重点将是限价订单和市价订单。

> [发现并回顾最佳交易自动化软件](https://coincodecap.com/category/trading-automation)

# 限价订单

限价订单是以特定价格或更好的价格出售或购买加密货币的订单。在限价订单的情况下，必须指定订单的价格和数量。

虽然这似乎是一个简单的概念，不能进一步细分，但我们发现交易所已经实施了多种类型的限价单。两种最常见的限价订单是“取消前有效”(GTC)和“立即或取消”(国际奥委会)订单。

这两种订单都以指定的价格和数量下单，但 GTC 订单在交易所中是未平仓的，直到它们被交易员取消或被接受未平仓订单的另一个市场参与者填充。

与 GTC 的订单不同，国际奥委会的订单一旦下达，交易所就会立即取消。订单中未完成的任何数量将被退回给交易商。国际奥委会的订单通常是通过接受交易而立即执行的。

> 虽然我们不会深究何时使用这些不同订单类型的策略，但在制定您的交易策略时，最好记住这些差异。

## CCXT 示例

对于我们的第一个例子，我们将探索如何使用 CCXT 库在 Bittrex 交易所执行限价单。为了成功地连接交易所并执行订单，我们需要首先生成 API 密钥来验证我们对交易所的访问。

[**按照本指南中的步骤 1–3 生成您的 Bittrex API 密钥。**](https://help.shrimpy.io/en/articles/2438628-how-to-link-bittrex-api-keys)

准备好 API 密钥后，使用下面的脚本，将 API 密钥分配给适当的“apiKey”和“secret”字段。

```
import ccxtbittrex = ccxt.bittrex({
    'apiKey': '...',
    'secret': '...',
})# fetch your account balances for each asset
account_balance = bittrex.fetch_balance()# execute the order
limit_order_placement =  bittrex.create_limit_buy_order('ETH/BTC', '0.01', '0.014'))
```

> 注意:我们已经对“ETH/BTC”交易的价格进行了硬编码。为了确定执行交易的准确价格点，最好评估 Bittrex 上订单簿的当前状态，以选择交易的具体价格和数量。因为这是一个示例，所以不建议使用默认值。

为了自动化[交易](http://blog.coincodecap.com/tag/trading)的过程，我们需要从交易所收集订单簿数据，以评估我们应该在哪里下单。

```
account_balance = bittrex.fetch_balance()orderbook = bittrex.fetch_order_book('ETH/BTC')
```

有关如何收集订单簿数据的更多信息，请参见我们的最新教程:

[](https://blog.shrimpy.io/blog/ccxt-crypto-exchange-order-book-snapshot) [## CCXT 加密交换订单簿数据[示例教程] | Shrimpy

### 订单簿指定了交易所中当前开放的所有可用订单。每个订单代表一个…

blog.shrimpy.io](https://blog.shrimpy.io/blog/ccxt-crypto-exchange-order-book-snapshot) 

## 简单的例子

由于 CCXT 库的限制，我们还将探讨如何使用 [**Shrimpy 通用加密交易 API**](https://developers.shrimpy.io/)来实现这一点。

> 为了完成下面的例子，用 [**Shrimpy 加密数据&交易 API**](https://developers.shrimpy.io/)注册一个账户。登录到 Shrimpy 后，通过选择“创建 Api 主密钥”按钮来生成主 API 密钥。从该过程中生成的密钥将在以下脚本中使用。您还需要购买至少最低的用户订阅层来执行交易。

## 安装

在进入我们的 Shrimpy 示例之前，让我们从安装 Shrimpy Python 库开始。

```
pip install shrimpy-python
```

## 例子

现在库已经安装好了，让我们构建我们的脚本，它将在 Bittrex 上执行交易。同样，如果您需要 [**生成新的 Bittrex API 密钥，请遵循本指南中的步骤 1–3。**](https://help.shrimpy.io/en/articles/2438628-how-to-link-bittrex-api-keys)

```
import shrimpy
import time# use your Shrimpy API public and private keys to create the client
public_key = '...' # shrimpy_public_key (aka Shrimpy Master Key)
secret_key = '...' # shrimpy_secret_key (aka Shrimpy Master Key)client = shrimpy.ShrimpyApiClient(public_key, secret_key)# create a new user
user = client.create_user(
    'mycustomname' # (optional) name
)# link up to 20 exchange accounts for each user
account = client.link_account(
    user['id'], # user_id
    'bittrex',  # exchange
    '...',      # public_key (aka exchange apiKey)
    '...'       # private_key (aka exchange secretKey)
)# access balance data for each of your user's accounts
balance = client.get_balance(
    user['id'],   # user_id
    account['id'] # account_id
)# sleep for 5 seconds the first time linking an account
time.sleep(5)# place an IOC or GTC limit order for your user
place_limit_order_response = client.place_limit_order(
    user['id'],    # user_id
    account['id'], # account_id
    'ETH',         # base_symbol
    'BTC',         # quote_symbol
    '0.01',        # quantity of base_symbol
    '0.026',       # price
    'SELL',        # side
    'IOC'          # time_in_force ('IOC' or 'GTC')
)
```

为了执行精确的交易，我们需要从交易所收集精确的订单簿数据。这可以通过下面的简单例子来实现。

```
orderbooks = client.get_orderbooks(
    'bittrex', # exchange
    'ETH',     # base_symbol
    'BTC',     # quote_symbol
    10         # limit of how much depth to return on each side 
)
```

> 注意:这个交易示例只执行单个交易，如果您想使用限价单将您的整个投资组合交易到一个资产，Shrimpy 支持端点来完成这一操作。

您可以使用以下请求来分配投资组合。

```
client.allocate(
    '701e0d16-1e9e-42c9-b6a1-4cada1f395b8', # user_id
    123,                                    # account_id
    {
        'isDynamic': False,
        'allocations': [
            { 'symbol': 'BTC', 'percent': '100' }
        ]
    }
)
```

***轰！就像那个***[***Shrimpy***](https://shrimpy.io/referral?r=I6VFZ7d2E)***会自动翻遍你的整个投资组合，把你所有的东西卖给*** [***比特币***](https://blog.coincodecap.com/a-candid-explanation-of-bitcoin) ***。这甚至可以处理基础货币和 BTC 之间没有交易对的边缘情况。***

# 市场订单

执行市价单比不同类型的限价单更容易掌握。市场订单不是以特定的价格下单，而是简单地以给定时间的最佳价格下单，直到订单金额被满足。

> 这意味着如果你想卖出 LTC 来购买 BTC，交易所将继续以市场价格卖出 LTC，直到达到你指定的订单数量。

## CCXT 示例

我们的第一个例子将介绍如何用 CCXT 实现这一点。像以前的脚本一样，它将要求我们输入 API 公钥和密钥，以便连接到我们想要的交换。

```
import ccxtbittrex = ccxt.bittrex({
    'apiKey': '...',
    'secret': '...',
})# get account balances to determine how much can be traded
account_balance = bittrex.fetch_balance()# sell .001 ETH for market price and receive BTC right now
market_order_placement = bittrex.create_market_sell_order('ETH/BTC', .001)
```

为了制定买卖加密货币的完整策略，我们需要获取更多的市场数据来做出交易决策。例如，如果我们想要基于一项资产的当前市场价格触发交易，我们将需要访问跟踪每项资产价格的实时价格行情。

在上一篇文章中，我们讨论了如何使用 CCXT 构建加密价格报价器。

[](https://blog.shrimpy.io/blog/ccxt-live-crypto-exchange-price-ticker) [## CCXT 实时加密交易所报价器[示例教程] | Shrimpy

### 我们将介绍两种不同的方法来生成实时价格行情。第一个策略是访问数据…

blog.shrimpy.io](https://blog.shrimpy.io/blog/ccxt-live-crypto-exchange-price-ticker) 

## 微不足道的例子

既然我们已经演示了如何使用 CCXT 执行市场订单，那么让我们构建一个脚本来展示如何使用 [**Shrimpy 加密交易 API**](https://developers.shrimpy.io/)来执行。

> 注意:如果您遵循了上一个 Shrimpy 限价单示例，那么您应该已经创建了一个用户，并且已经将一个 exchange 帐户链接到了您的 Shrimpy 开发人员 API 密钥。这意味着对于下一个示例，我们不需要创建另一个用户或链接一个新的 exchange 帐户。您可以简单地检索您以前创建的用户和余额数据，以执行更多的交易。

```
import shrimpy# use your Shrimpy API public and private keys to create the client
public_key = '...'
secret_key = '...'client = shrimpy.ShrimpyApiClient(public_key, secret_key)# note: since we created a user in our last example script, 
# we can just retrieve our list of users.
users = client.list_users()
first_user_id = users[0]['id']# retrieve the accounts associated with this user
accounts = client.list_accounts(
    first_user_id
)
first_account_id = accounts[0]['id']# access balance data for the user account you previously created
balance = client.get_balance(
    first_user_id,   # user_id
    first_account_id # account_id
)# execute a market order
smart_order_response = client.create_trade(
    first_user_id,    # user_id
    first_account_id, # account_id
    'BTC',            # from_symbol
    'ETH',            # to_symbol
    '0.01'            # amount of from_symbol
)
```

请注意如何访问与用户相关联的所有帐户。如果您为一个用户链接了 1 个币安、3 个 Bittrex、2 个 KuCoin 和 4 个北海巨妖 exchange 帐户，则可以通过同一个用户访问它们，而无需每次重新链接密钥。这使您可以轻松地管理成千上万的用户，而不必为每个用户维护 API 密钥。

## 智能交易

作为奖励，我们将提供一个智能订单路由的例子。如果不仔细看，您可能会认为下一个脚本与上一个示例相同。但是，如果您仔细观察“创建交易”的请求，您会注意到添加了一个参数，它自动允许我们执行智能订单路由策略。

> *太简单了！*

```
import shrimpy# use your Shrimpy API public and private keys to create the client
public_key = '...'
secret_key = '...'client = shrimpy.ShrimpyApiClient(public_key, secret_key)# note: since we created a user in our last example script, 
# we can just retrieve our list of users.
users = client.list_users()
first_user_id = users[0]['id']# retrieve the accounts associated with this user
accounts = client.list_accounts(
    first_user_id
)
first_account_id = accounts[0]['id']# access balance data for the user account you previously created
balance = client.get_balance(
    first_user_id,   # user_id
    first_account_id # account_id
)# execute a market order
smart_order_response = client.create_trade(
    first_user_id,    # user_id
    first_account_id, # account_id
    'BTC',            # from_symbol
    'ETH',            # to_symbol
    '0.01'            # amount of from_symbol
    True              # enable smart_routing
)
```

> 请注意 Shrimpy 如何通过一个请求执行完整的智能订单路由(SOR)策略。不需要不断地重新评估市场，也不需要在整个交易所谨慎下单，以执行完美的 SOR 策略。Shrimpy 会为你做所有的重活！

今天试试 [**虾米交易&数据 API**](https://developers.shrimpy.io/)。这是在加密货币交易所开始交易的最简单方式。收集历史市场数据，访问实时 websockets，执行高级交易策略，并管理无限数量的用户。

*   [**Shrimpy Python 库**](https://github.com/shrimpy-dev/shrimpy-python)
*   [**Shrimpy Node.js 库**](https://github.com/shrimpy-dev/shrimpy-node)

不要忘记在 [Twitter](https://twitter.com/ShrimpyApp) 和[脸书](https://www.facebook.com/ShrimpyApp)上关注我们的更新，并在 [Telegram](https://t.me/ShrimpyGroup) 上向我们令人惊叹的活跃社区提出任何问题。

捕虾队

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)