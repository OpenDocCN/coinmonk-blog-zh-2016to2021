# Uniswap V3 解释-集中流动性，非永久性损失，滑脱

> 原文：<https://medium.com/coinmonks/uniswap-v3-explained-concentrated-liquidity-impermanent-loss-slippage-529cf16c3509?source=collection_archive---------1----------------------->

![](img/03269fcef0840587e04fbd8b095371b2.png)

Uniswap 协议是一种 ETH 本机智能合同系统，支持对 ERC20 <> ERC20 和 ERC20 <> ETH 进行交换。

Uniswap 使用自动做市商(AMM)算法来执行交易。用户以成对的令牌提供流动性，以创建流动性池。通过将提供的令牌放入池中并从池中取出请求的令牌来执行交易。互换费用适用于分配给流动性提供者(LPs)的 ask 令牌金额。

[Uniswap V3](https://uniswap.org/blog/uniswap-v3/) 是引入了集中流动性和许多其他概念的协议的最新版本。在 V3 中，根据提供流动性的风险，有几个可用的费用级别。这些费用在游泳池的 2 个代币中收取，不会再投资到游泳池中。

UNI 是 Uniswap 协议的治理令牌。UNI 令牌持有者将来可能有资格享受[协议费](https://docs.uniswap.org/concepts/V3-overview/fees#protocol-fees)。当前方案费用为 0%。UNI 令牌持有者可以更改协议费用。

# 集中流动性

Uniswap V3 使用的是[集中流动性](https://docs.uniswap.org/concepts/V3-overview/concentrated-liquidity)做市商(CLMM)，这是一种比标准不变产品做市商(CPMM)算法更有效的市场标记算法。

池中有 2 个令牌令牌 0 和令牌 1。令牌 0 的价格(P)以令牌 1 表示。例如，在 UNI <> ETH 池中，每 1 个条目 100 个 UNI。

在 CLMM 中，LPs 必须选择一个他们提供流动性的价格范围。如果价格 P 超出了某个池的范围，它就会变得不活跃，并使用已更改价格范围内的下一个可用池来执行交换。

在 CLMM 中，资金池跟踪资金池中价格 (P)和流动性(L)的[平方根。计算交换中接收的令牌量不需要池中的令牌量。](https://uniswap.org/whitepaper-v3.pdf)

下面是定义代币数量、价格和流动性之间关系的公式。

```
# x is the amount of token0, y is the amount of token1
# price of token0 in terms of token1
P = y / x

# liquidity is the geometric mean of the amount of tokens
L = sqrt(x*y)
```

在 V3 中，流动性被定义为给定平方根 p 变化的令牌 1 数量变化。基于这一概念，下面的 V3 公式被用来计算您可以获得的令牌数量。

```
Δy = Δ(√P) * L 
Δx = Δ(1/√P) * L
```

以上公式用于计算每个相邻刻度的价格变动。刻度是一个整数，用下面的公式表示价格。

```
P = 1.0001^i
sqrt(P) = 1.0001^(i/2)
i = log(sqrt(P)) * 2 / log(1.0001)
```

每个刻度线与相邻刻度线相距 0.1%。如果完全交换的价格变动超过了相邻的分笔成交点，则在从一个分笔成交点移动到另一个分笔成交点的分步功能中执行交换，直到所有的代币都被交换。

对于两个相邻分笔成交点内的价格变动，CLMM 遵循常数乘积公式。CLMM 是常数乘积公式的变体。

下面是我写的一个脚本，用来模拟使用集中流动性公式的互换。我忽略了交换中的申请费。仅实施了令牌 1 到令牌 0 的交换。

在 V3 中，流动性池被表示为 NFT，因为每个池彼此不同。基于互换的价格影响，单个互换可能从一个池转移到另一个池。

与标准的常数乘积算法相比，集中流动性非常高效。CLMM 在池的价格范围内使用池中的全部流动性。但是 CPMM 将流动性从 0 分散到无穷大。这是因为 CLMM 有不同的公式来计算池的新状态。

# 价格影响

当对池进行交换时，池中的令牌比率会发生变化。令牌池中令牌的比率是令牌 0 相对于令牌 1 的价格(P)。

在交换开始时，投票的比例是 100:1。但是，当与 1ETH 交换时，您将不会获得 100UNI，因为池的比率会发生变化。这被称为互换的[价格影响](https://docs.uniswap.org/concepts/introduction/swaps#price-impact)。

让我们以 UNI <> ETH 流动性池为例。当前比率为 100 单位/1 小时。我们将使用 CPMM 的 V2 公式，因为计算要容易得多，但 V3 的概念仍然相同。

```
# x and y are number of tokens
# x_uni = 10000, y_eth = 100
x_uni * y_eth = k
(x_uni - recieve) * (y_eth + deposit) = k
(10000 - receive) * (100 + 1) = 10000 * 100
receive = 10000 - (10000 * 100 / 101)
receive = 99.0099
```

在上面的计算中，您可以看到第 1 个 ETH 有 99.0099 个 UNI 令牌。令牌池中的令牌比率已发生变化，但令牌数量的乘积仍保持不变。

# 滑动

具有较高 gas 的交易可以在具有较低费用的交易之前执行。不可能预测事务将在哪个时间点执行。在事务广播和执行之间，池的状态可能已经改变。资金池状态的改变可能会导致掉期价格与预期价格大相径庭。这种价格变化被认为是[滑点](https://docs.uniswap.org/concepts/introduction/swaps#slippage)。

# 非永久性损失

流动性提供者通过提供流动性来承担风险。池中代币的比例将根据当前市场价格不断变化。套利者将与资金池交易，以使资金池令牌比率(价格)与更大市场的比率相匹配。这种投资组合的再平衡对有限合伙人来说是有风险的，因为当他们决定撤回资金时，比率可能会非常偏向已经失去价值的代币。

让我们举个例子来看看这一点。下面的例子使用 V2 CPMM，因为它有一个简单的公式，但概念也适用于 V3。

爱丽丝和鲍勃决定资助 BTC 游泳池。我们将看到流动性池在不同时间的状态。使用两个等式计算池的状态。

```
# token_x and token_y are number of tokens
# k is the constant product and r is the ratio of tokens
token_x * token_y = k
token_x / token_y = r
# substituting the value of token_y
token_x^2 / r = k
token_x = √(k*r)
token_y = √(k/r)
```

token_x = BTC，token_y =瑞士

**在 T0**
r = 1/10
初始池状态= 900 BTC + 9000 ETH
爱丽丝存入 100 BTC + 1000 ETH
最终池状态= 1000 BTC + 10000 ETH
爱丽丝是池的 10%所有者

**在 T1**
r = 1/8
初始池状态= 1118 BTC + 8944 ETH
鲍勃存款 80 BTC + 640 ETH
最终池状态= 1198 BTC + 9584 ETH
鲍勃拥有总池的 6.67%
爱丽丝现在拥有池的 9.33%

**在 T2**
r = 1/5
初始池状态= 1515.36 BTC + 7576.8 ETH

爱丽丝决定退出资金池
爱丽丝将获得资金池的 9.33%，即 141.38 BTC + 706.91 ETH。按现行汇率相当于 282.76 BTC。如果爱丽丝持有代币而不是添加到池中，那么她将拥有 100 BTC + 1000 以太币，按照当前的汇率相当于 300 BTC。所以爱丽丝损失了 17.24 BTC。

最终池状态= 1373.98 BTC + 6869.89 瑞士法郎

Bob 现在拥有资金池的 7.356%，并决定将资金留在资金池中。

**在 T3**
r = 1:8
初始资金池状态= 1086.22 BTC + 8689.76 ETH
鲍勃决定从资金池中提取资金
鲍勃将获得资金池的 7.356%，即 79.9 BTC + 639.218 ETH。按照目前的汇率，这相当于 159.8 BTC(考虑到十进制误差，这 160 等于 159.8)。如果 Bob 没有在池中存款，他将拥有 80 BTC + 640 瑞士法郎，按照当前汇率，这相当于 160 BTC。这里我们看到 Bob 没有损失任何价值，因为池的比率与他存放代币时相同。

这就是它被称为非永久性损失的原因。如果该池的比率与您存放代币时的比率相同，则没有损失。

有限合伙人从每笔交易中获得交易费。如果有限合伙人收取的交易费用大于非永久性损失，那么有限合伙人可以从资金池中提取资金并获利。

> 加入 Coinmonks 电报频道，了解加密交易和投资

## 另外，阅读

*   [电网交易机器人](https://blog.coincodecap.com/grid-trading)
*   [密码交易机器人](https://blog.coincodecap.com/best-crypto-trading-bots)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)