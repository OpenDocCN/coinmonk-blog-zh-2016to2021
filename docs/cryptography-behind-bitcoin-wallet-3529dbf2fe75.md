# 比特币钱包背后的密码学

> 原文：<https://medium.com/coinmonks/cryptography-behind-bitcoin-wallet-3529dbf2fe75?source=collection_archive---------11----------------------->

![](img/647af4c26c7f21ff8f64bf781b973f4a.png)

Image taken from [here](https://miro.medium.com/max/1400/1*4tSEKx4KzavZZDAggIoJVA.png)

在我的上一篇博客中，我讨论了块头的概念，以及块的散列是如何计算的。概念上的实现可以在 CTF 挑战赛中找到**做第二件事**。如果你错过了 CTF，那么你可以在[https://medium . com/@ the _ harvester/does-a-second-matter-10f 5984 e 5061](/@the_harvester/does-a-second-matter-10f5984e5061)上阅读这篇文章。在这篇博客中，我将讨论如何计算比特币区块链的钱包地址。这个概念还涉及到很多密码学。

# 1.钱包地址是什么？

因为我们在各自的银行都有一个账号，如果有人给你汇款，他会把钱汇到你的银行账号。这个号码对你来说是独一无二的，世界上没有其他人会在同一家银行拥有相同的账号。同样，在比特币交易中，如果有人想将比特币发送给任何其他拥有比特币账户的人，那么他必须将比特币发送到该人的钱包地址。在比特币区块链，没有账号的概念，而是硬币被发送到一个人钱包的地址。因为它是一个数字概念，所以它有公钥和私钥的实现。钱包地址是在私钥的帮助下生成的，该私钥只有钱包的所有者知道。这个私钥用于访问钱包，如果它被所有者丢失，那么他将永远无法使用比特币来访问各自的钱包。从私钥生成公钥，从公钥生成钱包地址，并分发给需要向您发送硬币的各方。重要的事实是，该过程是单向的，即，你不能从公钥获得私钥，也不能从钱包地址获得公钥。这就是数学的魔力。

# 2.你的钱包地址是怎么算出来的？

让我们在示例的帮助下一步一步地查看计算过程:

1.  该过程从生成 256 比特的随机私钥开始:**010101010101001001010010010001011101010101010101010101010101010101010101010101010101010101010101010101**
2.  私钥被转换为十六进制格式。在十六进制格式中，有一对 4 个二进制数字。所以 256 位变成了 256/4 = 64 个十六进制字符:**555252 a 522 eaad 5556 ad 4 abaaaad 555 aaab 555 AAAA 55492 ad 555 AAAA ab 55555**
3.  这种十六进制格式用作私钥，并使用**椭圆曲线加密 secp256k1** 推荐参数生成公钥。要了解这种加密技术的更多信息，请阅读 pdf(【https://www.secg.org/sec2-v2.pdf】)文件，其中陈述了加密过程中使用的必要参数。
4.  上述私钥的公钥为**04932867 ea 2d 1685 B4 f 645 DD 274 f 14677 CD 9 F8 ce 7 FB 3 B1 c 7 a 7787 c 661526 Fe 39 a 6474481 b 2719 c 8 EC 95 Fe 4a 920 e 297d 83 D5 B4 c 08 c 83 a 55 e 69d 2e 98531 EC 78808**，包含 x 坐标和 y 坐标。前缀 04 表示它是未压缩的公钥，因此为了进一步计算，取 x 轴，即**02932867 ea 2d 1685 B4 f 645 DD 274 f 14677 CD 9 F8 ce 7 FB 3 B1 c 7 a 7787 c 661526 Fe 39 a 6**，前缀 02 表示它是压缩的公钥。
5.  这个公钥要经过多次哈希运算才能得到钱包地址。现在，让我们来看看将在其上执行的各种操作
6.  对公钥执行字节式 SHA256 哈希。基于字符的哈希和基于字节的哈希是有区别的。基于字符的哈希将给出**b 99 e 52 a 27 e 91 ee 74 ad 038 D2 d 25 f 6821 a 974 e 34 c 55 ba 01482 a 55 a 50 af2 b**，但是基于字节的哈希将给出**64936245d 2412 a 249d 02 a 35968 fab 260 ef 0 ea 2 CB 410 a 695 c 158 c 09 ee 55d 4 b 7**作为输出。
7.  下面提到了做同样事情的代码:
    [https://gist . github . com/ansh vaid 4/a 8d 67 c 48d 2e 0 f 28 fc 0526913 DC 970757 . js](https://gist.github.com/AnshVaid4/a8d67c48d2e0f28fc0526913dc970757.js%22></script>)
8.  RIPEMD160 散列您在逐字节 SHA256 散列后得到的输出。使用上面的代码，将 SHA256 哈希的名称改为 RIPEMD160。同样用 SHA256 散列的输出来改变 **pubkey** 变量中的输入字符串。输出如下:**568 db 6 f 20 e 28 e 415 c8e 788 af 37 BBB d24 c 12d 67 e 3**
9.  现在，从 RIPEMD160 hash 中获取上面的输出，在开头添加两个 0，然后进行字节式 SHA256 散列，然后再次对您从之前的字节式 SHA256 散列中获得的输出进行字节式 SHA256 散列。输出如下:**3 f 75 c 800 Fe 0 be 4 be 6 a4 b 634797d 661 b 580001 e7b 910 bb 4 ad 466 BC 4 DC 2492 e 019**。
10.  从上一次哈希得到的输出开始处取前 4 个字节，即 **3f75c800** 。记住它是十六进制的，因此一个字符是一组 4 个二进制数，这就是为什么 8 个十六进制字符被认为是 4 个字节。
11.  这 8 个字节是校验和，添加到开头有两个 0 的 RIPEMD160 哈希的末尾。因此哈希现在变成了**00568 db 6 f 20 e 28 e 415 c8e 788 af 37 BBB d24 c 12d 67 e 33 f 75 c 800**。这就是所谓的 25 字节比特币钱包地址。但这不是真正的钱包地址。
12.  对其执行字节 Base58 编码以获得钱包地址。输出是:**18 Tet 19 jftjbf C5 NH Xu 8 B1 cnb 5 jmoq 4 vs**，这是与我们随机创建的 256 位私钥相关联的实际钱包地址。

*在这篇博客中，我讨论了比特币区块链的钱包地址是如何创建的。概念上的实现可以在 CTF 挑战赛* ***我的钱包地址*** *中找到。如果你错过了 CTF，那么你可以在*[https://medium . com/@ the _ harvester/whats-my-wallet-address-a 8 c 25 c 8 e 32 b 1](/@the_harvester/whats-my-wallet-address-a8c25c8e32b1)*阅读这篇文章。在下一篇博客中，我将讨论一些新概念。*

> 社交媒体链接:[LinkedIn](https://www.linkedin.com/in/ansh-vaid/)|[GitHub](https://github.com/AnshVaid4)|[insta gram](https://www.instagram.com/being_optimist/)|[Twitter](https://twitter.com/anshvaid4)
> 
> 访问我的网站:[https://cybergeeks.website/](https://cybergeeks.website/)
> 
> *原载于*[*https://github.com*](https://github.com/AnshVaid4/Blogs/blob/caff161e83f7cb87d41468660ab5875de2cd47a4/blogs/2021-10-13-ExploitingServices.md)*。*

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 也阅读

[](https://blog.coincodecap.com/crypto-exchange) [## 最佳加密交易所| 2021 年十大加密货币交易所

### ICON _ PLACEHOLDEREstimated 预计阅读时间:28 分钟加密货币交易所的加密交易需要知识…

blog.coincodecap.com](https://blog.coincodecap.com/crypto-exchange) [](https://blog.coincodecap.com/crypto-lending) [## 2021 年 10 大最佳加密贷款平台| CoinCodeCap

### 当谈到加密货币贷款时，大量因素等同于良好的收入状况。此外，借款的一部分…

blog.coincodecap.com](https://blog.coincodecap.com/crypto-lending) [](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [## 2021 年最佳免费加密交易机器人

### 2021 年币安、比特币基地、库币和其他密码交易所的最佳密码交易机器人。四进制，位间隙…

medium.com](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) [## 最佳 4 个加密交易信号电报通道

### 这是乏味的找到正确的加密交易信号提供商。因此，在本文中，我们将讨论最好的…

medium.com](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) [](https://blog.coincodecap.com/blockfi-review) [## BlockFi 评论 2021:利弊和利率| CoinCodeCap

### 今天，我们提出了一个全面的 BlockFi 评论，这是一个成立于 2017 年的加密贷款平台，拥有其…

blog.coincodecap.com](https://blog.coincodecap.com/blockfi-review) [](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) [## 如何在印度购买比特币？2021 年购买比特币的 7 款最佳应用[手机版]

### 如何使用移动应用程序购买比特币印度

medium.com](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) [](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b) [## 加密税务软件——五大最佳比特币税务计算器[2021]

### 不管你是刚接触加密还是已经在这个领域呆了一段时间，你都需要交税。

medium.com](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b) [](https://blog.coincodecap.com/best-hardware-wallet-bitcoin) [## 存储比特币的最佳加密硬件钱包[2021] | CoinCodeCap

### 保管您的数字资产很容易，但找到正确的存储方式却是一项繁琐的任务。在线钱包有一个风险…

blog.coincodecap.com](https://blog.coincodecap.com/best-hardware-wallet-bitcoin)