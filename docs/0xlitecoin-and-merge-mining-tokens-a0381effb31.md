# 0xLitecoin 和合并挖掘以太币令牌

> 原文：<https://medium.com/coinmonks/0xlitecoin-and-merge-mining-tokens-a0381effb31?source=collection_archive---------10----------------------->

![](img/b656c4f6d64e43382cd5d2da52fd4afc.png)

http://eips.ethereum.org/EIPS/eip-918 的 EIP 918 可挖掘令牌标准有一个合并挖掘的规范，但是至今还没有在主网上实现。

我已经发布了基于 EIP918 规范的合并挖掘的第一个实现。如下所述的文章当前在 [http://mike.rs](http://mike.rs) Mike.rs 的池中并且存在于该智能合同中:

*   [https://ethers can . io/address/0x 33d 99 EFC 0 C3 cc 4 f 93 da 6931 EC 2 cccf 19 ca 874 b 6d #事件](https://etherscan.io/address/0x33d99efc0c3cc4f93da6931ec2cccf19ca874b6d#events)
*   [https://github . com/sni SSN/0x litecoin-token/blob/master/contracts/_ 0x litecoin token . sol](https://github.com/snissn/0xlitecoin-token/blob/master/contracts/_0xLitecoinToken.sol)

# **什么是合并挖掘？**

**合并挖掘**是允许基于同一算法的两种不同加密货币同时被挖掘**的过程**。0xBitcoin 的矿工如果他们的矿池支持合并采矿，可以自动成为 0xBitcoin 和 0xLitecoin 的矿工。此外，该标准允许同时合并挖掘许多令牌，从而允许挖掘以太坊令牌的新爆炸。

# 从技术上讲，合并挖掘是如何工作的？

**先解释一下 0x 比特币挖矿:**

当矿工开采 0x 比特币时，他们会发现一个 [Nonce](https://en.wikipedia.org/wiki/Cryptographic_nonce) (一个只能使用一次的任意数字)，当与矿工的以太坊地址(池的地址，或者如果单独开采，单独矿工的地址，或者如果使用了薄荷助手合同，薄荷助手合同的地址)和 0x 比特币的当前挑战散列时。重复选择随机数，使得随机数、eth 地址和质询在散列在一起时小于合同的挖掘目标。挖掘目标与难度成反比(高难度，低挖掘目标)

用 python 编写的用于为所挖掘的令牌 0xbitcoin 寻找有效随机数的通用挖掘函数如下:

```
def mine(challenge, public_address, mining_target):
  while True:
    nonce = generate_random_number()
    hash1 = int(sha3.keccak_256(challenge+public_address+nonce).hexdigest(), 16)
    if hash1 < mining_target:
      return nonce, hash1
```

合并挖掘是如何工作的？

0xLTC 实际上使用了与 0xBitcoin 相同的挖掘算法和完全相同的挑战。这意味着 0xBitcoin 的有效 nonce(解)可以被 0xLTC 契约完全接受，反之亦然。打个比方，0xLTC 和 0xBTC 的令牌都使用相同的“锁”锁定在契约内部，并且都可以使用相同的密钥解锁。

一个挖掘池可以处理这方面的技术实现，因此运行任何链接到此处的[开源令牌挖掘器的令牌挖掘器只需将他们的挖掘器指向该池，他们将挖掘 0xBitcoin、0xLTC 和该池支持的任何未来合并挖掘令牌！](https://www.reddit.com/r/0xbitcoin/comments/8o06dk/links_to_the_newestbest_miners_for_nvidia_amd/)

**为什么，下一步是什么？**

0xLitecoin 的“为什么”是为了展示如何创建新代币，即:

*   分发到，不要分散矿工对 0x 比特币的注意力
*   没有 ICO，也不需要代币收入者的任何资本支出
*   为未来可挖掘令牌设置新标准，以取代 ICO

我认为，当一个平台的网站在做 ICO，而不是选择用 0xBitcoin 进行令牌合并挖掘时，令牌合并挖掘标准将取得成功，推出自己的兼容开源池，并在不需要潜在欺诈的 ICO 路径的情况下，通过实用令牌取得成功。

**为什么取名为 0xLitecoin？**

莱特币与比特币非常相似，主要区别在于挖掘算法、允许更快转移的阻塞时间以及 10 分钟内分发 4 倍硬币的想法。我想推出一个不同于 0xBitcoin 的合并挖掘令牌，并消除差异。0xLitecoin 每个 0xBitcoin mint 分发的代币是 4 倍，就像 litecoin vs 比特币一样。

**附加阅读**

这个栈溢出写了合并挖掘如何为比特币和命名币工作是非常清楚的[https://bitcoin.stackexchange.com/a/275](https://bitcoin.stackexchange.com/a/275)记住令牌挖掘有一些不同，因为令牌挖掘不直接保护区块链，而是工作有助于令牌分发。

# tl/dr

*   有一种叫做 [0xLitecoin](https://0xltc.org) 的全新代币与 [0xBitcoin](https://0xbitcoin.org) 合并开采
*   在 Mike.rs 的池中挖掘 0xBTC 也会给你 0xLTC(更多的池在后面)
*   在创建 0xBTC 块时，可以向 0xLTC 协定发送一条消息，该消息也释放 0xLTC 块，这就是令牌合并挖掘。
*   随着新的可挖掘令牌的创建，如果它们与 0xBitcoin 合并挖掘，它会将它们的哈希能力完全保留在 0xBitcoin 上，并且不会降低新令牌的 0xBitcoin 的哈希能力。
*   0xLTC 的难度和 0xBTC 一样，所以没有像其他新的可开采代币那样的天然气竞标战