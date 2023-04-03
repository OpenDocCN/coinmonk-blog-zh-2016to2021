# 从头开始创建和签署 SegWit 事务

> 原文：<https://medium.com/coinmonks/creating-and-signing-a-segwit-transaction-from-scratch-ec98577b526a?source=collection_archive---------2----------------------->

逐步地

我们之前[从头开始创建并签署了一个 P2PKH 交易](/@ottosch/manually-creating-and-signing-a-bitcoin-transaction-87fbbfe46032)。这一次，我们将创建一个 SegWit 事务，包含来自以下地址的 3 个输入:

*   [**mxfehesxxkjy 9 ycmfzxnpue 3 ffjyby 56 ja**](https://mempool.space/testnet/address/mxFEHeSxxKjy9YcmFzXNpuE3FFJyby56jA):P2PKH；
*   [**TB 1 Qt 9 xzu 0 df 95 vs fal 8 eptzyruv 4 e 00k 4 ty 6d 8 zhh**](https://mempool.space/testnet/address/tb1qt9xzu0df95vsfal8eptzyruv4e00k4ty6d8zhh):p 2 wpkh；
*   [**2n 4 yehdwic 9 TM 4 br n9 EP 1 hnsu 9 F6 cwjru 31**](https://mempool.space/testnet/address/2N4yEhDwic9Tm4BRN9EP1hnSu9f6cWJrU31):P2SH-p 2 wpkh；

一个遗产，一个 SegWit 和一个包装 SegWit。是的，这使它变得更加复杂，但它将有助于理解事务序列化、签名过程以及如何组合 legacy 和 SegWit 输入中的差异。

下表总结了我们的 UTXOs:

从总数 16，091，satoshis 中，我们将把除矿工费(2000 sats)之外的所有内容发送到地址**TB 1 qeds 7 u 3 tgpqkttxkzdwukaj 8 muqgf 5 nqq 6 w05 AK**。这次我会尽量简洁，跳过一些细节。如果需要的话，我推荐阅读[之前的教程](/@ottosch/manually-creating-and-signing-a-bitcoin-transaction-87fbbfe46032)以获得更详细的解释。

![](img/5dc5e4ce6e0a9af16a8e600d798576a0.png)

It seems I need a pointless image to get a header — sorry, n00b here

# 1.生成私钥

让我们从生成 PEM 私钥开始:

我们现在有以下私钥文件:`priv-legacy.pem`、`priv-segwit.pem`和`priv-wrapped.pem`。

# 2.事务序列化

如果一个事务至少有一个 SegWit 输入，本地的或包装的，那么它就是一个 SegWit 事务。此类事务的序列化不同于遗留事务。实际上，这有点复杂，因为我们有一种签名格式和一种完全不同的推送格式。
而且，由于我们有一个遗留输入，并且这些输入是使用旧格式签名的，我们还必须构建一个遗留事务。因此，这一次我们将不得不进行 3 种不同的序列化:

*   遗留序列化，对我们的遗留输入进行签名；
*   SegWit one，用于签署每个 SegWit 输入；
*   最后序列化，以推动交易；

我们可以从任何输入开始。让我们先做传统的。

# 3.签署遗留输入

我们首先必须序列化事务，就像我们要推送遗留事务一样。在我们获得遗留输入的签名后，我们丢弃这个事务并处理 SegWit 输入。

使用版本 2，有 3 个输入，我们从:

```
02000000 03
```

然后，对于遗留输入，我们得到[先前的事务](https://mempool.space/testnet/tx/d1a92ad68a031c5324981aa920152bd16975686905db41e3fc9d51c7ff4a20ed) n ID:

并且输出索引`01000000`:

```
ed204affc7519dfce341db0569687569d12b1520a91a9824531c038ad62aa9d1 01000000
```

对于 scriptSig，我们添加上一个事务的 scriptPubKey 及其大小:`19 76a914b780d54c6b03b053916333b50a213d566bbedd1388ac`。在添加了序列`ffffffff`(没有 RBF 或 locktime)之后，我们的第一个输入完成了:

```
ed204affc7519dfce341db0569687569d12b1520a91a9824531c038ad62aa9d101000000**1976a914b780d54c6b03b053916333b50a213d566bbedd1388acffffffff**
```

其他输入是 SegWit，但这不会改变任何东西，因为对于遗留签名，其他 scriptSigs 应该是空的。因此，对于本机 SegWit 输入，在收集了 [UTXO 信息](https://mempool.space/testnet/tx/b7203bd59b3c26c65699251939e1e6353f5f09952156c5b9c01bbe9f5372b89c)之后，我们有:

```
9cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b7 01000000 00 ffffffff
```

最后，对于[嵌套的 SegWit UTXO](https://mempool.space/testnet/tx/04d984cdcf728975c173c45c49a242cedee2da5dc200b2f83ca6a98aecf11280) 也是如此:

```
8012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d904 01000000 00 ffffffff
```

我们与投入的交易变成:

```
0200000003**ed204affc7519dfce341db0569687569d12b1520a91a9824531c038ad62aa9d1010000001976a914b780d54c6b03b053916333b50a213d566bbedd1388acffffffff9cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b70100000000ffffffff8012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d9040100000000ffffffff**
```

对于输出部分，我们有一个单一的输出，所以`0x01`。我们正在传输 16，089，269 个 satoshis，因此让我们使用 Python 的 REPL 来获得 little-endian 中的字节数:

我们将发送到地址**TB 1 qeds 7 u 3 tgpqkttxk zdwukaj 8 mu qgf 5 nqq 6 w 05 AK**。P2WPKH 的 scriptPubKey 是`OP_0 <pubkey hash>`。在验证事务时，当节点在被花费的输出中发现该模式时，它们将其识别为 P2WPKH，并添加与 P2PKH 相同的操作码，将其转换为`OP_DUP OP_HASH160 <pubkey hash> OP_EQUALVERIFY OP_CHECKSIG`。

使用类似 [bech32 demo](https://bitcoin.sipa.be/bech32/demo/demo.html) 或 [bech32-buffer](https://slowli.github.io/bech32-buffer/) 的网络工具，解码地址以获得公钥哈希:`cb61ee4568082cb59ac26bb96ec8fbe0109a4c00`。然后添加推送操作`0x14`和 scriptPubKey 大小`0x16` ，我们得到了输出:

```
b580f50000000000 16 00 14 cb61ee4568082cb59ac26bb96ec8fbe0109a4c00
```

在加上输出数量`0x01`和输出本身之后，我们的事务变成:

```
0200000003ed204affc7519dfce341db0569687569d12b1520a91a9824531c038ad62aa9d1010000001976a914b780d54c6b03b053916333b50a213d566bbedd1388acffffffff9cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b70100000000ffffffff8012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d9040100000000ffffffff**01b580f50000000000160014cb61ee4568082cb59ac26bb96ec8fbe0109a4c00**
```

最后，添加 locktime `00000000`和 sighash `01000000`，我们就可以签名了。这是我们要签署的:

```
0200000003ed204affc7519dfce341db0569687569d12b1520a91a9824531c038ad62aa9d1010000001976a914b780d54c6b03b053916333b50a213d566bbedd1388acffffffff9cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b70100000000ffffffff8012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d9040100000000ffffffff01b580f50000000000160014cb61ee4568082cb59ac26bb96ec8fbe0109a4c00**0000000001000000**
```

## 3.1 签名

让我们`hash256`交易并签字:

签名的细节我就不赘述了。请参考[上一篇文章](/@ottosch/manually-creating-and-signing-a-bitcoin-transaction-87fbbfe46032)。这一次，我真的厌倦了重复的工作，所以我写了一个小的 [Python 脚本来修复签名](https://gist.github.com/ottosch/339aafe8dd5284a47e624b44fba70426)。尽管我没有明确地编写它，但我已经用`fix-signature.py`管道化了所有的签名命令。

追加 sighash `0x01`后，我们有了第一个签名:

```
304402200da2c4d8f2f44a8154fe127fe5bbe93be492aa589870fe77eb537681bc29c8ec02201eee7504e37db2ef27fa29afda46b6c331cd1a651bb6fa5fd85dcf51ac01567a01
```

让我们保存它，并继续下一个输入。

# 4.签署 SegWit 输入

是时候签署其他意见了。 [BIP143](https://github.com/bitcoin/bips/blob/master/bip-0143.mediawiki#Specification) 为签名 SegWit 输入定义了新的交易格式:

```
Double SHA256 of the serialization of:
 1\.  nVersion of the transaction (4-byte little endian)
 2\.  hashPrevouts (32-byte hash)
 3\.  hashSequence (32-byte hash)
 4\.  outpoint (32-byte hash + 4-byte little endian) 
 5\.  scriptCode of the input (serialized as scripts inside CTxOuts)
 6\.  value of the output spent by this input (8-byte little endian)
 7\.  nSequence of the input (4-byte little endian)
 8\.  hashOutputs (32-byte hash)
 9\.  nLocktime of the transaction (4-byte little endian)
 10\. sighash type of the signature (4-byte little endian)
```

这里也有一些新的说法，我们会看到的。让我们从原生输入开始。

## 4.1 签名输入#1

在添加了版本`02000000`之后，我们有了新的东西:之前输出点的散列。outpoint 是前一个事务 ID 和输出索引的串联。我们已经从 UTXOs 中提取了这些数据，所以让我们对其进行哈希运算:

迄今为止的序列化:

```
0200000099197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e6
```

然后，哈希序列:

我们有:

```
0200000099197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e6**82a7d5bb59fc957ff7f737ca0b8be713c705d6173783ad5edb067819bed70be8**
```

现在是 outpoint，它特定于这个输入:

```
0200000099197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e682a7d5bb59fc957ff7f737ca0b8be713c705d6173783ad5edb067819bed70be8**9cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b701000000**
```

然后是 scriptCode，在 P2WPKH 的例子中是`1976a914 <pubkey hash> 88ac`。这个和 P2PKH 的 scriptPubKey 一样。使用 bech32 工具之一获得 pubkey 散列后，我们添加脚本代码:

```
0200000099197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e682a7d5bb59fc957ff7f737ca0b8be713c705d6173783ad5edb067819bed70be89cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b701000000**1976a914594c2e3da92d1904f7e7c856220f8cae5efb556488ac**
```

然后输入值:9300 sats，是`5424000000000000`，后面是顺序`ffffffff`:

```
0200000099197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e682a7d5bb59fc957ff7f737ca0b8be713c705d6173783ad5edb067819bed70be89cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b7010000001976a914594c2e3da92d1904f7e7c856220f8cae5efb556488ac**5424000000000000ffffffff**
```

hashOutputs，它是所有输出的散列。每个输出都是`amount || scriptPubKey size || scriptPubKey`。我们只有一个，所以:

我们就快到了:

```
0200000099197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e682a7d5bb59fc957ff7f737ca0b8be713c705d6173783ad5edb067819bed70be89cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b7010000001976a914594c2e3da92d1904f7e7c856220f8cae5efb556488ac5424000000000000ffffffff**f3ae23c3fd63a2e0479888f95c7a8ab221b20add6ac819e9d8953edd1a0cd924**
```

最后，locktime 和 sighash:

```
0200000099197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e682a7d5bb59fc957ff7f737ca0b8be713c705d6173783ad5edb067819bed70be89cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b7010000001976a914594c2e3da92d1904f7e7c856220f8cae5efb556488ac5424000000000000fffffffff3ae23c3fd63a2e0479888f95c7a8ab221b20add6ac819e9d8953edd1a0cd924**0000000001000000**
```

我们准备好签字了。

**4.1.1 签约** 这里没什么新鲜的。和以前一样，只需重复散列和签名:

现在加上 sighash `0x01`就有了第二个签名:

```
3045022100f8dac321b0429798df2952d086e763dd5b374d031c7f400d92370ae3c5f57afd0220531207b28b1b137573941c7b3cf5384a3658ef5fc238d26150d8f75b2bcc61e701
```

## 4.2 签名输入#2

前 3 项(version、hashPrevouts 和 hashSequence)与最后一个输入中的相同:

```
02000000 99197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e6 82a7d5bb59fc957ff7f737ca0b8be713c705d6173783ad5edb067819bed70be8
```

然后是这个输入的输出点:

```
0200000099197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e682a7d5bb59fc957ff7f737ca0b8be713c705d6173783ad5edb067819bed70be8**8012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d90401000000**
```

现在，脚本代码。这个输入也是 P2WPKH，所以`1976a914 <pubkey hash> 88ac`:

```
0200000099197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e682a7d5bb59fc957ff7f737ca0b8be713c705d6173783ad5edb067819bed70be88012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d90401000000**1976a9146a721dcca372f3c17b2c649b2ba61aa0fda98a9188ac**
```

然后，我将立即添加输入值(16，029，969 = `1199f40000000000`)和序列`ffffffff`:

```
0200000099197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e682a7d5bb59fc957ff7f737ca0b8be713c705d6173783ad5edb067819bed70be88012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d904010000001976a9146a721dcca372f3c17b2c649b2ba61aa0fda98a9188ac**1199f40000000000ffffffff**
```

最后是 hashOutputs、locktime 和 sighash，我们已经从前面的输入中获得了它们:

```
0200000099197e88ff743aff3e453e3a7b745abd31937ccbd56f96a179266eba786833e682a7d5bb59fc957ff7f737ca0b8be713c705d6173783ad5edb067819bed70be88012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d904010000001976a9146a721dcca372f3c17b2c649b2ba61aa0fda98a9188ac1199f40000000000ffffffff**f3ae23c3fd63a2e0479888f95c7a8ab221b20add6ac819e9d8953edd1a0cd9240000000001000000**
```

**4.2.1 签名** 一如既往，双哈希，签名并追加 sighash:

这是我们最后一个 sighash 签名:

```
304402204ebf033caf3a1a210623e98b49acb41db2220c531843106d5c50736b144b15aa02201a006be1ebc2ffef0927d4458e3bb5e41e5abc7e44fc5ceb920049b46f87971101
```

# 5.最终事务序列化

要推送的事务实际上是 [BIP144](https://github.com/bitcoin/bips/blob/master/bip-0144.mediawiki#Specification) 中描述的一种新格式。和老格式差不多。
我们从版本开始:

```
02000000
```

然后我们有了两个新的字段，SegWit 标记符`0x00`和标志符`0x01`，这允许节点将其识别为 SegWit 事务:

```
02000000**0001**
```

然后，输入。我们有`0x03`。除了 scriptSig 之外，它们和以前一样，script SIG 取决于输入类型。对于遗留输入，我们已经知道它是`<sig> <pubkey>`。与推送操作一起，带大小的 scriptSig 变成:

```
6a 47 304402200da2c4d8f2f44a8154fe127fe5bbe93be492aa589870fe77eb537681bc29c8ec02201eee7504e37db2ef27fa29afda46b6c331cd1a651bb6fa5fd85dcf51ac01567a01 21 0242BF11B788DDFF450C791F16E83465CC67328CA945C703469A08E37EF0D0E061
```

添加输出点和序列，我们完成了遗留输入:

```
**ed204affc7519dfce341db0569687569d12b1520a91a9824531c038ad62aa9d101000000**6a47304402200da2c4d8f2f44a8154fe127fe5bbe93be492aa589870fe77eb537681bc29c8ec02201eee7504e37db2ef27fa29afda46b6c331cd1a651bb6fa5fd85dcf51ac01567a01210242BF11B788DDFF450C791F16E83465CC67328CA945C703469A08E37EF0D0E061**ffffffff**
```

本机 SegWit 输入更简单，因为 scriptSig 是空的。签名和公钥将出现在见证部分。第二输入:

```
9cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b7 01000000 00 ffffffff
```

包装的 SegWit 需要在 scriptSig 中推送救赎脚本，因为这是一个 P2SH。嵌套的脚本是一个 P2WPKH，所以是`OP_0 <pubkey hash>`。包括 pubkey 哈希的 push 操作码`0x14`和 redeemScript 本身的`0x16`，不要忘记脚本大小`0x17`，我们最终得到:

```
8012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d904 01000000 17 1600146a721dcca372f3c17b2c649b2ba61aa0fda98a91 ffffffff
```

关于签名和公钥位置的相同说明也适用于此处。这是到目前为止的交易，添加了输入的数量和输入本身:

```
020000000001**03ed204affc7519dfce341db0569687569d12b1520a91a9824531c038ad62aa9d1010000006a47304402200da2c4d8f2f44a8154fe127fe5bbe93be492aa589870fe77eb537681bc29c8ec02201eee7504e37db2ef27fa29afda46b6c331cd1a651bb6fa5fd85dcf51ac01567a01210242BF11B788DDFF450C791F16E83465CC67328CA945C703469A08E37EF0D0E061ffffffff9cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b70100000000ffffffff8012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d90401000000171600146a721dcca372f3c17b2c649b2ba61aa0fda98a91ffffffff**
```

输出部分与遗留事务中的相同。我们已经有了第一个签名，所以:

```
02000000000103ed204affc7519dfce341db0569687569d12b1520a91a9824531c038ad62aa9d1010000006a47304402200da2c4d8f2f44a8154fe127fe5bbe93be492aa589870fe77eb537681bc29c8ec02201eee7504e37db2ef27fa29afda46b6c331cd1a651bb6fa5fd85dcf51ac01567a01210242BF11B788DDFF450C791F16E83465CC67328CA945C703469A08E37EF0D0E061ffffffff9cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b70100000000ffffffff8012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d90401000000171600146a721dcca372f3c17b2c649b2ba61aa0fda98a91ffffffff**01b580f50000000000160014cb61ee4568082cb59ac26bb96ec8fbe0109a4c00**
```

我们已经接近尾声了。现在，在锁定时间之前，我们有了一个新的部分。见证部分是我们放置见证的地方，需要解决 SegWit UTXOs 的 scriptPubKey。这是交易中打折的部分(使得 SegWit 交易更便宜),并且允许超过 1MB 的块大小。由于遗留节点不接收见证节点，因此仍然有可能在 1MB 的限制内产生一个块，从而使块大小增加到可回溯兼容。

每个输入都需要自己的见证项数组。每个输入必须指定:

*   项目的数量；
*   项目大小；
*   项目本身；

第一个输入不需要任何条目，因为验证所需的一切都已经在 scriptSig 中了；因此，我们只需要项目的数量:`0x00`。第二个和第三个输入各有两项，分别是签名和公钥。
这里是我们交易的见证字段:

```
00
---
02
48 3045022100f8dac321b0429798df2952d086e763dd5b374d031c7f400d92370ae3c5f57afd0220531207b28b1b137573941c7b3cf5384a3658ef5fc238d26150d8f75b2bcc61e701
21 025972A1F2532B44348501075075B31EB21C02EEF276B91DB99D30703F2081B773
---
02
47 304402204ebf033caf3a1a210623e98b49acb41db2220c531843106d5c50736b144b15aa02201a006be1ebc2ffef0927d4458e3bb5e41e5abc7e44fc5ceb920049b46f87971101
21 02AE68D299CBB8AB99BF24C9AF79A7B13D28AC8CD21F6F7F750300EDA41A589A5D
```

添加见证:

```
02000000000103ed204affc7519dfce341db0569687569d12b1520a91a9824531c038ad62aa9d1010000006a47304402200da2c4d8f2f44a8154fe127fe5bbe93be492aa589870fe77eb537681bc29c8ec02201eee7504e37db2ef27fa29afda46b6c331cd1a651bb6fa5fd85dcf51ac01567a01210242BF11B788DDFF450C791F16E83465CC67328CA945C703469A08E37EF0D0E061ffffffff9cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b70100000000ffffffff8012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d90401000000171600146a721dcca372f3c17b2c649b2ba61aa0fda98a91ffffffff01b580f50000000000160014cb61ee4568082cb59ac26bb96ec8fbe0109a4c00**0002483045022100f8dac321b0429798df2952d086e763dd5b374d031c7f400d92370ae3c5f57afd0220531207b28b1b137573941c7b3cf5384a3658ef5fc238d26150d8f75b2bcc61e70121025972A1F2532B44348501075075B31EB21C02EEF276B91DB99D30703F2081B7730247304402204ebf033caf3a1a210623e98b49acb41db2220c531843106d5c50736b144b15aa02201a006be1ebc2ffef0927d4458e3bb5e41e5abc7e44fc5ceb920049b46f879711012102AE68D299CBB8AB99BF24C9AF79A7B13D28AC8CD21F6F7F750300EDA41A589A5D**
```

最后，添加锁定时间后，我们就完成了交易！

```
02000000000103ed204affc7519dfce341db0569687569d12b1520a91a9824531c038ad62aa9d1010000006a47304402200da2c4d8f2f44a8154fe127fe5bbe93be492aa589870fe77eb537681bc29c8ec02201eee7504e37db2ef27fa29afda46b6c331cd1a651bb6fa5fd85dcf51ac01567a01210242BF11B788DDFF450C791F16E83465CC67328CA945C703469A08E37EF0D0E061ffffffff9cb872539fbe1bc0b9c5562195095f3f35e6e13919259956c6263c9bd53b20b70100000000ffffffff8012f1ec8aa9a63cf8b200c25ddae2dece42a2495cc473c1758972cfcd84d90401000000171600146a721dcca372f3c17b2c649b2ba61aa0fda98a91ffffffff01b580f50000000000160014cb61ee4568082cb59ac26bb96ec8fbe0109a4c000002483045022100f8dac321b0429798df2952d086e763dd5b374d031c7f400d92370ae3c5f57afd0220531207b28b1b137573941c7b3cf5384a3658ef5fc238d26150d8f75b2bcc61e70121025972A1F2532B44348501075075B31EB21C02EEF276B91DB99D30703F2081B7730247304402204ebf033caf3a1a210623e98b49acb41db2220c531843106d5c50736b144b15aa02201a006be1ebc2ffef0927d4458e3bb5e41e5abc7e44fc5ceb920049b46f879711012102AE68D299CBB8AB99BF24C9AF79A7B13D28AC8CD21F6F7F750300EDA41A589A5D**00000000**
```

我们来测试一下…

Yeah!

# 6.结论

唷…我们已经走了很长一段路，但我们完成了。我们已经从头开始创建并签署了一个 SegWit 事务。我们还了解了新旧交易格式之间的差异、签名流程的不同以及如何将 legacy 和 SegWit UTXOs 结合起来。

希望你喜欢它，下次再见！

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资