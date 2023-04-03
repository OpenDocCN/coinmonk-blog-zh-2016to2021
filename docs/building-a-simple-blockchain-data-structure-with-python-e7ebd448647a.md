# 用 Python 构建简单的区块链数据结构

> 原文：<https://medium.com/coinmonks/building-a-simple-blockchain-data-structure-with-python-e7ebd448647a?source=collection_archive---------0----------------------->

![](img/96cfe938c8f4a85b528f88ff03d56dab.png)

Photo by Mateusz Dach from Pexels

在这里，我将建立一个简单的区块链数据结构，这是比特币的基础。这种数据结构甚至不足以构建简单的加密货币。但是我们必须从某个地方开始。

在构建区块链数据结构之前，我必须解释一下散列法。比特币使用 SHA-256。以下是如何用 Python 实现这一点:

```
>>> import hashlib
>>> hashlib.sha256(b”hello world”).hexdigest()
‘b94d27b9934d3e08a52e52d7da7dabfac484efe37a5380ee9088f7ace2efcde9’
```

这就是你在 Python 中如何做散列(SHA-256)。但是实际上哈希是什么呢？256 在 SHA-256 到底意味着什么？

散列是一个将任何东西(只要你能把它表示成一个字符串)转换成一个固定的 256 位字符串的过程。在前面的示例中，字符串“hello world”的长度为 11。事实上，“你好世界”的长度取决于你如何计算它。但为了简单起见，我们只统计有多少个字符。那个“hello world”字符串变成了固定大小的字符串，就是‘b 94d 27 b 9934d 3 e 08 a 52 e 52d 7 da 7 dab fac 484 EFE 37 a 5380 ee 9088 f 7 ace 2 EFC de 9’。假设我散列另一个不同长度的字符串。

```
>>> import hashlib
>>> hashlib.sha256(b”I am the best president. Ever.”).hexdigest()
‘fba0e4cc233f9df81278130ed748a31a842bce8f911766b661f8a4f7ddff5341’
```

那串“我是最好的总统。永远不会。”与“hello world”的长度不同。但是输出具有相同的长度，大约 64 个字符。所以任何输入都会变成 64 个随机字符串。即使是一个 23 公里长的字符串也会变成一个 64 个随机字符的字符串。

这是一个十六进制字符串。这就是为什么它有 64 个字符。如果你把它转换成一个位串，它将有 256 个字符长。

```
>>> bin(0xb94d27b9934d3e08a52e52d7da7dabfac484efe37a5380ee9088f7ace2efcde9)
‘0b1011100101001101001001111011100110010011010011010011111000001000101001010010111001010010110101111101101001111101101010111111101011000100100001001110111111100011011110100101001110000000111011101001000010001000111101111010110011100010111011111100110111101001’
```

这就是它被称为 SHA-256 的原因。

现在，哈希的一些特性对比特币非常重要。第一种叫做无碰撞。我的意思是，如果你要把任何东西变成一个固定的 256 位字符串，肯定会有不止一个输入有相同的输出。可能输入的大小大于可能输出的大小。是的，没错。但是找到 x 和 y 其中 x 不同于 y 并且 hash(x)等于 hash(y)是极其困难的(在 SHA-256 的情况下；一些聪明人在 SHA-1 发现了碰撞)。

所以输入和输出之间没有明显的关系。即使你改变了输入的一点点，输出也会完全不同。这是第二个属性。

```
>>> hashlib.sha256(b”1").hexdigest()
‘6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b’
>>> hashlib.sha256(b”2").hexdigest()
‘d4735e3a265e16eee03f59718b9b5d03019c07d8b6c51f90da3a666eec13ab35’
>>> hashlib.sha256(b”3").hexdigest()
‘4e07408562bedb8b60ce05c1decfe3ad16b72230967de01f640b7e4729b49fce’
>>> hashlib.sha256(b”11").hexdigest()
‘4fc82b26aecb47d2868c4efbe3581732a3e7cbcc6c2efb32062c08170a05eeb8’
```

因此，找到具有相同输出的不同输入的唯一方法是，需要测试不同长度的所有字符组合。“abc”，“也许是一个 loooooong 字符串”，“17”等。完全不切实际。

但是有没有可能当你散列一些东西的时候，输出已经和“hello world”的散列一样了呢？或者只是任意两个不同的字符串，但具有相同的哈希？当然有。问题是这种可能性有多小。SHA-256 的输出大约有 2 种⁵⁶可能性。2 ⁵⁶有多大？115792089237316195423570985008687907853269984665640564039457584007913129639936.或者 1.15 e+77。或者大致是 10⁷⁷.如果你认为这个数字很大，但不是很大，我有一个坏消息要告诉你。在可观测的宇宙中(从你的椅子上可以从任何方向看到 460 亿光年的宇宙)，总原子是 10⁷⁸到 10⁸。[https://www.universetoday.com/36302/atoms-in-the-universe/](https://www.universetoday.com/36302/atoms-in-the-universe/)

我的电脑有 Nvidia Geforce 1080 Ti。它有 11.3 万亿次浮点运算(tera = 10)。翻牌是浮动操作。哈希是整数运算。所以是苹果对橘子。但是为了简单起见，假设散列也是一种浮动操作，并且每个散列需要 3000 次操作。所以我的显卡每秒可以计算 376666666 哈希。为了找到在[生日攻击](https://en.wikipedia.org/wiki/Birthday_attack)中描述的冲突，我们只需要计算 2 个⁸散列。假设这个星球上的每个人都有我的图形卡，我们一起计算碰撞攻击。这需要:

```
>>> 2**128 / (7000000000 * 3766666666.6666665)
1.2905778770705631e+19
```

这个数字比宇宙的年龄还要长(大约 10 ⁷秒)。【https://www.space.com/24054-how-old-is-the-universe.html 

要描述哈希算法，是相当费工夫的。但是有一天我会解释 SHA-256 背后的代码。现在你可以理解哈希的浩瀚，让我们继续。区块链就像一个链表，是很多计算机专业学生都知道的数据结构。让我们创建一个块。比特币中的第一块叫创世纪块。

```
import hashlib, jsonblock_genesis = {
 ‘prev_hash’: None,
 ‘transactions’: [1, 3, 4, 2]
}
```

事务代表… *井*的事务。在比特币中，会像“杰森向玛丽苏支付 2 btc”，“凯洛·雷恩向尤达支付 10 btc”一样。为了简单起见，我们只放普通整数。

我们对该块进行了序列化，因此可以对其进行哈希处理。

```
block_genesis_serialized = json.dumps(block_genesis, sort_keys=True).encode(‘utf-8’)
block_genesis_hash = hashlib.sha256(block_genesis_serialized).hexdigest()
```

现在我们有了另一个街区。

```
block_2 = {
 ‘prev_hash’: block_genesis_hash,
 ‘transactions’: [3, 3, 3, 8, 7, 12]
}
```

我们散列块 2。

```
block_2_serialized = json.dumps(block_2, sort_keys=True).encode(‘utf-8’)
block_2_hash = hashlib.sha256(block_2_serialized).hexdigest()
```

我们建造另一个街区。

```
block_3 = {
 ‘prev_hash’: block_2_hash,
 ‘transactions’: [3, 4, 4, 8, 34]
}
```

我们散列块 3。这将是最后一个街区，我保证。

```
block_3_serialized = json.dumps(block_3, sort_keys=True).encode(‘utf-8’)
block_3_hash = hashlib.sha256(block_3_serialized).hexdigest()
```

为了确保数据没有被篡改，我只需要检查最后一个块的哈希，而不是检查从 genesis 块到最后一个块的所有数据。如果不一样，就说明有人试图篡改数据。

```
import hashlib, jsonblock_genesis = {
 ‘prev_hash’: None,
 ‘transactions’: [1, 3, 4, 2]
}block_2 = {
 ‘prev_hash’: None,
 ‘transactions’: [3, 3, 3, 8, 7, 12]
}block_3 = {
 ‘prev_hash’: None,
 ‘transactions’: [3, 4, 4, 8, 34]
}def hash_blocks(blocks):
 prev_hash = None
 for block in blocks:
  block[‘prev_hash’] = prev_hash
  block_serialized = json.dumps(block, sort_keys=True).encode(‘utf-8’)
  block_hash = hashlib.sha256(block_serialized).hexdigest()
  prev_hash = block_hash return prev_hashprint(“Original hash”)
print(hash_blocks([block_genesis, block_2, block_3]))print(“Tampering the data”)
block_genesis[‘transactions’][0] = 3print(“After being tampered”)
print(hash_blocks([block_genesis, block_2, block_3]))
```

结果是:

```
Original hash
45eda4f7a76bf0f92a0acda2ce4752dfbe167473376f766f22d7ec68501cac40
Tampering the data
After being tampered
27d68dae05428be6aa244869196a481f431fca6645dd33c3df7a740afa03b7d9
```

这是区块链的基本。但这还不够。如何决定下一个要添加的块？你需要共识和工作证明。那么区块链中的区块结构就复杂多了。我们将在下一篇文章中讨论这个问题。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   杠杆代币的终极指南
*   [Bookmap 评论](https://coincodecap.com/bookmap-review-2021-best-trading-software) | [美国 5 大最佳加密交易所](https://coincodecap.com/crypto-exchange-usa)
*   [如何在 FTX 交易所交易期货](https://coincodecap.com/ftx-futures-trading) | [OKEx vs 币安](https://coincodecap.com/okex-vs-binance)
*   [CoinLoan 审查](https://coincodecap.com/coinloan-review) | [YouHodler 审查](/coinmonks/youhodler-4-easy-ways-to-make-money-98969b9689f2) | [BlockFi 审查](https://coincodecap.com/blockfi-review)
*   XT.COM 评论 | [币安评论](https://coincodecap.com/xt-com-review)