# 免费比特币取证-第二部分

> 原文：<https://medium.com/coinmonks/free-bitcoin-forensics-part-2-51bafd9e032a?source=collection_archive---------0----------------------->

![](img/7a8b4919a649e4b7f6efb00dab61c37e.png)

欢迎来到“免费比特币取证”的第 2 部分，你可以在这里找到文章的第一部分[。在这一部分中，我将探索一些更高级的 BTC 取证技术和概念。本文将探讨以下主题:](https://pagan404.medium.com/free-bitcoin-analytics-part-1-6d3452df0b19)

*   交易图
*   用于规避/检测的区块链技术
*   钱包聚类
*   钱包指纹识别
*   脚本和地址格式
*   共同连接和支付连接
*   其他杂项识别模式
*   隐私的未来

所以，事不宜迟，让我们开始吧:

## **交易图**

为了有效地进行比特币取证，人们必须学会在跟踪交易时使用可视化工具，这是可以看到交易全貌的唯一方式。据我所知，唯一可用于可视化的免费工具是 [oxt.me](https://oxt.me/) ，它允许看到如下交易路径:

![](img/255111255ef73db12605fe8907e61e90.png)

[https://oxt.me/graph/transaction/tiid/3124363752](https://oxt.me/graph/transaction/tiid/3124363752)

要访问 [oxt.me](https://oxt.me/) 的可视化模式，只需点击“工具”按钮，在 oxt 中搜索任何交易后即可看到该按钮:

![](img/4262034058518b8afd5eb47d8fba5690.png)

[https://oxt.me/transaction/7a3fb18c76b3f54606b065209521f6a678692a592c8bcd7460d7dcf8d06faf19](https://oxt.me/transaction/7a3fb18c76b3f54606b065209521f6a678692a592c8bcd7460d7dcf8d06faf19)

然后展开您认为合适的事务图:

![](img/ddcaeabc95705b838dfeb3a749032a11.png)

事务图可以根据您的需要变得任意大:

![](img/d6c47a59269fcab2ef14ffbc729ba81e.png)

[oxt.me](https://oxt.me/) 还支持钱包聚类(尝试确定多个地址是否属于同一个实体)，网站会自动进行聚类，因此，如果您在某个地址旁边看到钱包名称(它们可能被称为 ANON……或者如果它已被编入索引，则有一个交易所名称),只需点击它即可查看钱包信息:

![](img/027ee2294d3d218a9d8e0c37f9b26449.png)![](img/99b61467d27e8394090f50fb8f9b0cc9.png)

[https://oxt.me/entity/tiid/2556015810](https://oxt.me/entity/tiid/2556015810)

我建议使用 [oxt.me](https://oxt.me/) 创建一个帐户，以获得额外的功能，如保存交易图表、留下评论以及能够查看与特定钱包相关的所有地址。

像这样可视化事务图可以为您提供一个惊人的大图视图，并允许您更快地检查许多事务(特别是在那些事务是重复的情况下)，但是您也应该注意单个事务视图的小图，并确保不要错过 coinjoins 或 exchange 事务，正确识别更改地址等等，否则您最终只会盲目地单击气泡，而不是从可视化中学习一些东西。

[research.oxt.me](https://research.oxt.me/) 完美地利用可视化来帮助他们进行超级深入的调查，他们是这种工具在实践中如何使用的一个很好的例子:[出版物](https://research.oxt.me/publications)

## **一些有趣的交易模式**

现在，我们已经熟悉了事务图，让我们来看看一些常见/有趣的事务模式，它们被用来增加或破坏隐私，以及它们在图上的样子。

剥离链—通常有一个输入和两个输出的一系列交易，其中一个输出(通常是较小的一个)被“剥离”以存放到第三方(如交易所)。另一个输出是在后续事务中继续“剥离链”的变化。

众所周知，黑客会使用剥离链方法，他们希望使自己的资金更难被追踪，并向他们想要洗钱的交易所隐藏资金的来源。"剥链"的概念甚至在美国对涉嫌参与几起交易所黑客攻击的个人的正式投诉中被提及:[https://www . justice . gov/opa/press-release/file/1253491/download](https://www.justice.gov/opa/press-release/file/1253491/download)

当[的 Twitter 黑客](https://en.wikipedia.org/wiki/2020_Twitter_bitcoin_scam)试图混淆他们被盗资金的流向时，也使用了 peel chain:
[https://cipher trace . com/Twitter-hack-update-区块链-analysis-helps-identify-Twitter-hackers/](https://ciphertrace.com/twitter-hack-update-blockchain-analysis-helps-identify-twitter-hackers/)

通过 [oxt.me](https://oxt.me/) 可视化的剥离链将如下所示:

![](img/2567c1884e9cddee5dd994ba08e263f4.png)

[https://oxt.me/graph/transaction/tiid/2923573673](https://oxt.me/graph/transaction/tiid/2923573673)

一般来说，我会说剥链在隐藏被盗资金或使被盗资金更难跟踪方面相当无效，尽管这可能有点耗时，但一个好的调查员将能够跟踪剥链交易。

[灰尘攻击](https://academy.binance.com/en/articles/what-is-a-dusting-attack) —名称“灰尘攻击”是指一种特殊的攻击，攻击者向他们希望跟踪的一个或多个实体的地址(通常为空)发送少量 BTC，希望该实体不会注意到他们地址中的小额新存款，并在另一次交易中无意中使用该地址中的资金，从而暴露更多他们以前未知的地址。这种攻击之所以有效，是因为大多数比特币钱包不提供让用户手动选择他们希望花费的未用产出的选项，而是自动选择，并且不区分“灰尘”未用产出和常规产出。

如果这种解释没有帮助，请参见下面来自 [bitcoingraphics.page](https://bitcoingraphics.page/) 的解释图。我发现该图对理解攻击非常有帮助:

![](img/4838209e0d5defb8e81e2ed77f5846cf.png)

灰尘攻击在比特币领域广为人知，因为 BTC 历史上曾发生过多次大规模的灰尘攻击。以下是灰尘攻击交易的几个例子:

*   [c 61e 8 ee 41d 8317 DC 0059 a 4 fa 2 CBD 5 b 55 c 75 c 9 AAC 521 c8 B3 d 47 b 763 F2 d 897 b 37](https://blockchair.com/bitcoin/transaction/c61e8ee41d8317dc0059a4fa2cbbd5b55c75c9aac521c8b3d47b763f2d897b37)
*   [c 9 f 5 ee 83627560 c 02 C5 da 5 f 52190 f 83d 3c 5 ef 46514 be 3 BC bfc 83365 cf 6 FEA 2d 7](https://blockchair.com/bitcoin/transaction/c9f5ee83627560c02c5da5f52190f83d3c5ef46514be3bcbfc83365cf6fea2d7)
*   [c 61e 8 ee 41d 8317 DC 0059 a 4 fa 2 CBD 5b 55 c 75 c 9 AAC 521 c8 B3 d 47 b 763 F2 d 897 b 37](https://oxt.me/transaction/c61e8ee41d8317dc0059a4fa2cbbd5b55c75c9aac521c8b3d47b763f2d897b37)

![](img/967adb0aec22f131ac1993144c9a59bf.png)

[https://oxt.me/transaction/c61e8ee41d8317dc0059a4fa2cbbd5b55c75c9aac521c8b3d47b763f2d897b37](https://oxt.me/transaction/c61e8ee41d8317dc0059a4fa2cbbd5b55c75c9aac521c8b3d47b763f2d897b37)

灰尘攻击交易的可视化是非常值得一看的…

交易跳跃——除了剥离链之外，另一种隐藏交易所资金来源的方法是通过一系列交易将资金发送到新创建的地址(当然是您自己的地址)。众所周知，交易所雇佣区块链的鉴证公司来确定到达交易所的资金来源，但这些鉴证公司使用的软件可能只扫描即将到来的资金参与的最近 3-4 笔交易(请注意，我在这里只是推测，我不知道最后有多少笔交易被扫描，不同的区块链鉴证公司之间肯定有所不同)。

你当然可以手动完成这种交易跳转，只需将你的资金发送到你新创建的未使用的地址几次，但也有一些钱包，如 Samourai wallet，原生支持这一功能: [ricochet](https://samouraiwallet.com/ricochet)

在[https://bitcoingraphics.page/](https://bitcoingraphics.page/)上，再次精彩讲解了交易跳转功能(在钱包中称为“跳弹”):

![](img/79b2b675f2f077b9ba93d9948359e559.png)

**钱包聚类**

至此，你可能已经问过自己，像 [oxt.me](https://oxt.me/) 和【walletexplorer.com】T2 这样的区块探索者是如何集群钱包的？虽然我不能 100%确定个人探索者是如何猜测钱包的，但肯定没有魔法，他们肯定使用了一个简单的假设，即交易中的所有输入都属于同一个钱包，并可能使用一些技巧来确定可能的更改地址。任何人都可以使用任何交易作为起点，对整个区块链进行同样的手动操作。我会这样做:

![](img/01597b69115dc93c58a45a77cfa9cf41.png)

[https://blockchair.com/bitcoin/transaction/d681640cfcbcec4b0b6ede9ce9aaaa6106158956e89cf3b5ed4f7e09b40b2630](https://blockchair.com/bitcoin/transaction/d681640cfcbcec4b0b6ede9ce9aaaa6106158956e89cf3b5ed4f7e09b40b2630)

假设我们从上面的交易开始。首先，由于共同的输入所有权启发，我们可以立即假设该事务中的两个输入地址属于同一个钱包。有了这些知识，下一个合乎逻辑的步骤就是更深入地探索它们:

*   [39 mrb KF 2 foblcoy 92 jy ct7 hab FDG 3y 6 dyx](https://blockchair.com/bitcoin/address/39mrBKf2foBLcoy92jyct7HAbfdg3y6dyX)
*   [3 jcsgfetd 5 r5 QM 8 dgg 6 bv HMF 5 v2 S4 ttr 4 l](https://blockchair.com/bitcoin/address/3JcSGFetd5R5QM8dgG6BvhmF5v2S4tTR4L)

在地址 39 mrb KF 2 foblcoy 92 jy CT 7 hab FDG 3y 6 dyx 的历史记录中，我们可以看到该地址参与了另一笔交易，其中它是存款地址之一:

![](img/c4870ca0705a867b8f1f012bb476611d.png)

[https://blockchair.com/bitcoin/transaction/8d6097bc1f743b8c280da44ca8d604c4fccd2c65532f521262fd6eff20ddce79](https://blockchair.com/bitcoin/transaction/8d6097bc1f743b8c280da44ca8d604c4fccd2c65532f521262fd6eff20ddce79)

由于该事务看起来不像 Coinjoin，我们可以假设这里的所有输入地址也属于同一个钱包。所以现在我们的钱包已经增长到以下地址:

*   [39 mrb KF 2 foblcoy 92 jy CT 7 hab FDG 3y 6 dyx](https://blockchair.com/bitcoin/address/39mrBKf2foBLcoy92jyct7HAbfdg3y6dyX)
*   [3 jcsgfetd 5 r5 QM 8 dgg 6 bv HMF 5 v2 S4 ttr 4 l](https://blockchair.com/bitcoin/address/3JcSGFetd5R5QM8dgG6BvhmF5v2S4tTR4L)
*   37 hfbmzumpen 8 ihv 28 ftwmyxvpqv 16 KP 7
*   38 gbwjuxvcbvwysemjzllgpcfy 8 df 4 wsag
*   [3 jo 5 vrdzcqtp 998 ekuuvcztteaae 3 NC 5 vt](https://blockchair.com/bitcoin/address/3Jo5vrdzCQtP998EkuUCvczTeAAe3nc5VT)
*   [31 ivvbnufumf 7 uf 17 hwep B4 xutfndnaisj](https://blockchair.com/bitcoin/address/31ivvBNUFuMF7Uf17HWePb4XUtFNDNAiSJ)
*   3 nuwtqodhj 4 f 893 dtmokkb 5 vxur 4 rgy rcu
*   [3 qnl 2 m2 osndudt 8 dkc qwjzsae 4 BH 1 wqeb](https://blockchair.com/bitcoin/address/3QnL2m2osNDUudT8dkcqWjzSAE4Bh1WqEb)
*   [39 mrb KF 2 foblcoy 92 jy ct7 hab FDG 3y 6 dyx](https://blockchair.com/bitcoin/address/39mrBKf2foBLcoy92jyct7HAbfdg3y6dyX)

接下来，我将检查新地址是否以同样的方式查看它们是否作为输入参与其他交易，以及它们是否将任何新发现的地址添加到列表中，检查它们，将新发现的输入地址添加到列表中，等等，直到所有这样的地址都被发现并添加到钱包地址列表中。

仅用这种技术就可以很好地近似钱包，但是如果可以检测到改变地址并将其添加到钱包地址列表中，则可以进一步改进近似。然后，还可以检查新检测到的改变地址，以查看它们是否作为输入包含在任何其他事务中，并且将也是该事务中的输入的任何地址添加到列表中。

最后，能够检测 coinjoins 将有助于钱包猜测，因为，显然 coinjoins 不遵循公共输入所有权启发式。

当然，所有这些都是非常自动化的，可以合理地假设，oxt.me 和 walletexplorer.com 都使用上述技术的某种组合来确定钱包。

**钱包指纹识别**

每一笔 BTC 交易都是独一无二的，仔细看的话，大多数交易都有很大的不同。事实上，它们差别很大，在某些情况下，可以确定交易是从哪个钱包进行的。为了说明这一点，我检查了几个钱包，并根据它们的一些特征制作了这张表:

![](img/b42fb16f2ef73a59390c3298e14457c2.png)

给定属于这些钱包之一的一些交易，确定这些交易属于哪个钱包并不困难(除非这些交易属于 Atomic 或 Guarda 钱包，因为它们具有相似的特征)。

虽然从这些例子来看，钱包指纹可能看起来微不足道，但实际上，由于存在数百个加密货币钱包(尽管我们在这里说实话，只有其中一些钱包足够受欢迎，值得关注)，并且这些钱包不断变化，随着钱包的变化，它们的交易也发生变化，因此实际情况非常复杂。任何希望有效地对钱包进行指纹识别的实体不仅必须跟踪大量的钱包，还必须跟上这些钱包的变化。据我所知，没有免费工具尝试这样的事情，我不知道专业的区块链取证工具(像 Chainanalysis 这样的公司使用的)是否能够做到这一点，但我假设如果不能，他们将在未来的某个时候开始至少对一些钱包进行指纹识别。

表中提到的特征并不是唯一可以用来识别钱包的特征。如果你对可以用来识别钱包的更多特征感兴趣，我推荐你阅读比特币隐私维基的以下部分:
[https://en.bitcoin.it/wiki/Privacy#Wallet_fingerprinting](https://en.bitcoin.it/wiki/Privacy#Wallet_fingerprinting)

## **脚本和地址格式**

在上一节中，我们展示了事务如何具有一些容易注意到的差异，这些差异有助于确定它们可能属于哪个钱包。这很棒，但很容易被注意到只能让你到此为止，要理解比特币交易的全部独特性以及它们之间的共同模式，我们需要仔细看看交易。本节将试图做到这一点，但在我们开始之前，我强烈建议阅读以下掌握比特币的章节(如果你还没有)，否则，要完全理解这里提供的信息将更加困难:
[https://github . com/bitcoinbook/bitcoinbook/blob/develop/ch06 . asciidoc](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch06.asciidoc)
[https://github . com/bitcoinbook/blob/develop/ch07 . asciidoc](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch07.asciidoc)

对于一个普通的比特币用户来说，似乎有 3 种比特币地址(以“1”开头的、以“3”开头的和以“bc1”开头的)。但另一种更正确的方式是，比特币有 6 种地址。一旦你开始探索“原始交易”，它们就变得显而易见了，也就是说，在区块链探索者使它们更容易跟踪之前，交易看起来是真实的。在[这里](/datadriveninvestor/bitcoin-raw-transaction-breakdown-c0a5a3aa8688)可以很好地了解原始交易的内容。

但是，在我们深入“原始事务”本身之前，让我们简要讨论一下所有地址类型:

[P2PHK](https://en.bitcoinwiki.org/wiki/Pay-to-Pubkey_Hash) (支付公钥哈希)——以“1”开头的原始比特币地址。它们只是公钥的散列(因此得名)。当比特币最初出现时，这是唯一可用的地址类型，因此它现在被最广泛地采用，几乎所有的交易所、钱包和其他比特币业务都接受并支持 P2PKH。然而，这种地址类型非常简单，不允许对地址做任何有趣的事情(例如进行 [multisig](https://en.bitcoin.it/wiki/Multisignature) 交易)，因此，在比特币开始工作后不久，一种新类型的地址被引入——P2SH。

[P2SH](https://en.bitcoin.it/wiki/BIP_0016) (支付到脚本哈希)—2012 年推出，P2SH 格式的比特币地址非常灵活，允许将支出条件嵌入到地址中(比特币协议支持数百个[操作码](https://en.bitcoin.it/wiki/Script#Opcodes)，从中可以创建支出条件，实际上，P2SH 最常见的新用法是 [multisig](https://en.bitcoin.it/wiki/Multisignature) )，输出的支出条件被哈希，从而被嵌入到地址中。因为地址只包含散列的花费条件(并且散列不能被反转)，所以只有当输出在所谓的“兑换代码”中被花费时，花费条件本身才会被揭示。如果你有兴趣了解更多关于如何在实践中创建 P2SH 地址的信息，我推荐你阅读这篇精彩的文章。

[p 2 wpkh](https://wiki.trezor.io/P2WPKH)(Pay-to-Wrapped-Script-Hash)和[P2WSH](https://wiki.trezor.io/P2WSH)(Pay-to-Wrapped-Script-Hash)——2017 年对比特币协议进行了 [SegWit](https://en.bitcoin.it/wiki/Segregated_Witness) 升级，以提高可扩展性并修复比特币中的交易延展性。作为此次升级的一部分，引入了一种新的地址类型，通常称为 Segwit 或 Bech32 地址。Bech32 地址实际上可以细分为两个不同的地址，即 P2WPKH 和 P2WSH 地址，尽管它们乍看起来很相似(都以“bc”开头)，但它们实际上与 P2PKH 和 P2SH 一样不同。P2WPKH 相当于 P2PKH 的 Segwit，而 P2WSH 相当于 P2SH。我们将很快深入了解这些地址之间的差异。

嵌套在 P2SH 中的 P2WPKH 和嵌套在 P2SH 中的 P2WSH——与 Bech32 地址一起，Segwit 升级还引入了一种在 P2SH 地址中“隐藏”Bech32 地址的聪明方法。这样做的好处是兼容性，很多交易所和钱包仍然不支持将 BTC 发送到 Bech32 地址，但几乎都支持将资金发送到 P2SH 地址。

现在，让我们看看“原始事务”以及这些地址类型在那里是什么样子。最“原始”的事务看起来像这样:

010000000144 f 9987226 FD 388 e 994446557 e 697507 be 0431 CCE 8 ca 50 c 8 f 26 b 59 f 5757 Fe 9 f 0000000008 b 483045022100 df 173 cf 465 c 319888425 b 5210 e 306 bdbf 92 e 7 a1 bfe 861626 FB 983066

但是这样不好，我们必须把这个交易从十六进制的解码成更可读的格式。同样的“解码”交易可以在这里查看:[720 EDC 9 c 452709 DBC 1d 49 b 93 Baba ef 8 DD 5 FB 813 3c 9 f 63 f 880 C1 fff 17061182 f](https://blockchain.info/rawtx/720edc9c452709dbc1d49b93babaef8dd5fb813c3c9f63f880c1fff17061182f)

![](img/1a48420cab07057b8129cf61f5ffd33b.png)

现在这更可行了，不是吗？如果我们只对输入和输出感兴趣，我们可以更进一步，使用只提供解码输入和输出信息的块浏览器:

*   [720 EDC 9 c 452709 DBC 1d 49 b 93 Baba ef 8d D5 FB 813 c 3c 9 f 63 f 880 C1 fff 17061182 f](https://blockstream.info/tx/720edc9c452709dbc1d49b93babaef8dd5fb813c3c9f63f880c1fff17061182f)
*   [720 EDC 9 c 452709 DBC 1d 49 b 93 Baba ef 8 DD 5 FB 813 c 3c 9 f 63 f 880 C1 fff 17061182 f](https://www.blockchain.com/btc/tx/720edc9c452709dbc1d49b93babaef8dd5fb813c3c9f63f880c1fff17061182f)

现在，让我们只关注输入/输出，并为此使用 [https://blockstream.info](https://blockstream.info/) 和[https://www.blockchain.com](https://www.blockchain.com/)资源管理器。

![](img/d01f6970e83fd088fad7ca244aba0d37.png)

这个交易简单得可以想象，因为它只是一个 P2PKH 地址将资金转移到另一个 P2PKH。从中，我们可以方便地看到所有 P2PKH 输入和输出的样子。

首先，让我们具体看一下输入:

![](img/d71e1d3700e1bd5114a620b7c69beaa2.png)

这里的 Pkscript(也称为“锁定脚本”)部分取自比特币起源的早期交易的输出，您将能够在该交易的输出中看到它:

*   [9 FFE 57579 FB 5268 f0ca 58 cebcc 3104 be 0775697 e 554644998 e 38 FD 267298 f 944](https://www.blockchain.com/btc/tx/9ffe57579fb5268f0ca58cebcc3104be0775697e554644998e38fd267298f944)

Pkscript 在 blockchain.info raw 事务中可见，在那里它被表示为以下字符串:76a 9146556 fee Fe 150168 a 4708 C2 E4 db 5a 5232 b6a 0910 a 88 AC

这仍然与“输入”屏幕截图中的 Pkscript 完全相同，只是一些代表操作码的代码字符被转换为人类可读的格式:

![](img/83a4b6fe04a5675db08581ad99f4ef6e.png)

如果您希望了解所有这些操作码的确切含义，并检查其他操作码的存在以及它们的十六进制代码，您可以在这里看到:
[https://docs . RS/sys coin/4 . 16 . 1/src/bitcoin/block data/opcodes . RS . html # 85](https://docs.rs/syscoin/4.16.1/src/bitcoin/blockdata/opcodes.rs.html#85)

您可能已经注意到，在 Pkscript 的 blockchain.info 分解中省略了 OP_PUSHCODE_20 操作码，这是一个相当标准的做法，因为这个操作码所做的只是准备下一个字符串(通常是 pubkey、pubkeyhash、signature……)被推送到堆栈上。如果这没有多大意义，没关系，反正也没那么重要。

Pkscript 是地址中的一个条件，需要满足该条件才能使用输出。在这种情况下，条件是证明您拥有私钥，公钥哈希(**6556 feefe 150168 a 4708 C2 E4 db 5a 5232 b 6 a 0910 a**)是从该私钥派生的。

这是通过 SigScript(也称为“解锁脚本”)实现的，它提供公钥本身(**0250863 ad 64 a 87 AE 8 a2 Fe 83 C1 af 1a 8403 CB 53 f 53 e 486d 8511 dad8 a 04887 e5b 2352**)和签名(**304402220230 e80 a 31317 e 74 fc 11 c 15c 5387 af 59825 e E5 e 6 f 277)**

对于 P2PKH 输入，见证(在 Segwit 升级期间添加的附加数据字段)保持为空。

![](img/18efe83ad6b6bf38d60e65437e0e8727.png)

输出只是提供了在未来交易中将会满足的下一个消费条件 Pkscript。

为了简化这一切，我们可以说 P2PKH 事务将遵循这些模式:

```
PkScript: *OP_DUP OP_HASH160 <public key hash> OP_EQUALVERIFY OP_CHECKSIG*
SigScript: *<signature> <public key>*
Witness: *N/A*
```

既然我们知道了这种模式，并且能够很容易地识别 P2PKH 输入和输出，我们可以看看其他地址类型留下了什么模式。

P2SH 是最难描述的，因为从技术上讲，任何条件(在合理范围内)都可以在 P2SH 脚本中看到，尽管它通常用于多签名事务，但 P2SH 地址的可能性几乎是无限的，因为它们可以使用操作码的任何组合来创建它们的锁定条件和解锁解决方案。P2SH 模式可以这样描述:

```
PkScript: *OP_HASH160 <redeem script hash> OP_EQUAL*
SigScript: *OP_0 <condition solution> OP_PUSHDATA1 <redeem script>*
Witness: *N/A*
```

这里的“赎回脚本”是地址上的一个任意条件。如果这是一个 2/3 的多重签名地址，则兑换脚本将如下所示:

```
Redeem script: *OP_PUSHNUM_2 <public key A> <public key B> <public key C> OP_PUSHNUM_3 OP_CHECKMULTISIG*
```

“条件解决方案”不是一个技术术语，我只是希望展示 P2SH 事务的模式。如果这是一个 multisig 交易,“条件解决方案”将是至少 2 个用私钥创建的签名，来自 redempt 脚本的公钥是从这些私钥中导出的。因此，假设 P2SH 地址是 2-of-3 multisig，事务部分将如下所示:

```
PkScript: *OP_HASH160 <redeem script hash> OP_EQUAL*
SigScript: *OP_0 <signature A> <signature B> OP_PUSHDATA1 OP_PUSHNUM_2 <public key A> <public key B> <public key C> OP_PUSHNUM_3 OP_CHECKMULTISIG*
Witness: *N/A*
```

你可以在这里探索这样的交易:

*   [02b 082113 e 35d 5386285094 c 2829 e 7 e 2963 fa 0b 5369 FB 7 f 4 b 79 C4 c 90877 DCD 3d？输出:0](https://blockstream.info/tx/02b082113e35d5386285094c2829e7e2963fa0b5369fb7f4b79c4c90877dcd3d?output:0)
*   [eeab 3 ef 6 cbea 5 f 812 B1 bb 8 b 8270 a 163 b 781 EB 7 CD 10 AE 5a 7d 8 a 3 f 452 a 57 DCA 93？输入:0](https://blockstream.info/tx/eeab3ef6cbea5f812b1bb8b8270a163b781eb7cde10ae5a7d8a3f452a57dca93?input:0)

其余的地址类型遵循与这两种非常相似的模式，只是部分内容有所变动。以下是他们的模式和一些交易:

## P2WPKH:

```
PkScript: *OP_0 <public key hash>*
SigScript: *N/A*
Witness: *<signature> <public key>*
```

交易示例:
[452 d61 CD 94212 CB 59 db 58169 a69ba 223745 b 099d 58 E8 fc 51 dded ee 16 E1 efdc 14](https://blockstream.info/tx/452d61cd94212cb59db58169a69ba223745b099d58e8fc51ddedee16e1efdc14?expand)

## P2WSH:

```
PkScript: *OP_0 <redeem script hash>*
SigScript: *N/A* Witness: *<condition solution> <redeem script>*
```

交易实例:
[CAE 0 CFD 93 f 8 defec 67795903 C1 D6 BBC 61 f 9 EAC 6892 EDB 9 EB 1d 104 FB 7 ee 47 a 877](https://blockstream.info/tx/cae0cfd93f8defec67795903c1d6bbc61f9eac6892edb9eb1d104fb7ee47a877)

## 嵌套在 P2SH 中的 P2WPKH:

```
PkScript: *OP_HASH160 <redeem script hash> OP_EQUAL*
SigScript: *OP_0 <public key hash>*
Witness: *<signature> <public key>*
```

交易示例:
[43b 31221 a 55 de 822 b 4503 e 667 c 29 a 98 FBA b5 fcf 6b 26 e 2291658 ff 24 e 6741 ea 29](https://blockstream.info/tx/43b31221a55de822b4503e667c29a98fbab5fcf6b26e2291658ff24e6741ea29)

## P2WSH 嵌套在 P2SH 中 **:**

```
PkScript: *OP_HASH160 <P2SH redeem script hash> OP_EQUAL*
SigScript: *OP_0 < P2WSH redeem script hash>*
Witness: *<condition solution> <P2WSH redeem script>*
```

交易示例:
[36 ff 5 db 60887547464 cf 7 fab 50 df 6539332751 BC CEC 03 CB 6d 55 FB 904d 128 f 879？展开](https://blockstream.info/tx/36ff5db60887547464cf7fab50df6539332751bccec03cb6d55fb904d128f879?expand)

我刚才讨论现有地址类型的原因是为了显示它们之间有多少变化，尤其是如果您包括嵌套在 P2SH 中的 P2SH、P2WSH 和 P2WSH 的所有变化，以及识别和分析这些地址有多容易。这种多样性本身就是一种隐私风险，存在的地址类型越多，识别个人钱包和个人用户就越容易。理想情况下，比特币应该只有一个地址，但现在秘密已经泄露了。无论如何，如果你有兴趣了解更多关于比特币脚本和地址的信息，我建议查看这些链接:
[https://blog . susanka . eu/types-of-bit coin-transactions-part-I/](https://blog.susanka.eu/types-of-bitcoin-transactions-part-i/)
[https://blog . susanka . eu/types-of-bit coin-transactions-part-ii-seg wit/](https://blog.susanka.eu/types-of-bitcoin-transactions-part-ii-segwit/)
[https://en.bitcoin.it/wiki/Script](https://en.bitcoin.it/wiki/Script)

在继续之前，我想在本节中简要介绍一下可以区分交易的其他一些因素:

[**【费用替代】**](https://bitcoinops.org/en/rbf-in-the-wild/) **:**

RBF 是比特币交易的一个特性，表示交易在内存池中时是可替换的。一些钱包提供这样的功能，而大多数没有，使得少数支持该功能的钱包脱颖而出。

如果某个事务的任何输入的序列号低于 0xffffffff — 1，则该事务可以发出支持 RBF 的信号。以下是此类交易的一个示例:

*   [17e 364 a 79 ECA 847d 077 c 0311 eed 61323 f 38 CB 39 CB 0 e 9d 3450979 c 120 bfea 40 bb](https://blockchair.com/bitcoin/transaction/17e364a79eca847d077c0311eed61323f38cb39cb0e9d3450979c120bfea40bb)
*   [1e 175480 a 3945 a 0 c 567 ddf 56 b 03 c 53 e 258179 AC 7912 e 08d 0e 4 DFD 9 b 2907 AC 684](https://blockchain.info/rawtx/1e175480a3945a0c567ddf56b03c53e258179ac7912e08d0e4dfd9b2907ac684)

![](img/5d4387da49b3fcd8e6e8c79e95a90e5e.png)

你可以在这里阅读更多关于选择加入 RBF [的信息](https://github.com/bitcoin/bips/blob/master/bip-0125.mediawiki):

## [](https://en.bitcoin.it/wiki/Timelock)****:****

**Locktime 设置可以挖掘事务的最早阻塞高度或时间。大多数钱包和交易所不使用这一功能，因此少数使用这一功能的钱包和交易所更容易被识别。可以在原始事务数据中看到锁定时间，如果锁定时间大于或等于 500000000，则被解释为 Unix 时间(从 1970 年 1 月 1 日开始的秒数)，如果小于该数字，则被解释为块高度。以下是一些启用了锁定时间的事务示例:**

*   **[0 fa 5a 64 BD 1 AC 65233 c 3883 b 010 BF 641988 fa 3f 4d 4755212 c 43243837920 ce 051](https://blockchair.com/bitcoin/transaction/0fa5a64bd1ac65233c3883b010bf641988fa3f4d4755212c43243837920ce051)**
*   **[5343 BD 9702 c0f 5b 0 a 37 c 86 c 32 E3 cc 375 c 24 FD 61 B4 d 392 B4 ce 48 e 2d 4a 99593 CB 4](https://blockchair.com/bitcoin/transaction/5343bd9702c0f5b0a37c86c32e3cc375c24fd61b4d392b4ce48e2d4a99593cb4)**

**![](img/2113ce97b61a1758ea434bd5628eece1.png)**

****在交易中写入信息的方法:****

**第一个比特币区块伴随着一条著名且永远正确的信息:**

**![](img/ac6078fc07b81a7a84fcf0d8b741db26.png)**

**[https://blockchair.com/search?q=4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b](https://blockchair.com/search?q=4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b)**

**此后，大量消息被写入比特币基地数据，但这并不是将消息留在区块链中的唯一方式，如果使用 OP_RETURN 操作码，还可以将它们留在输出中:**

**![](img/6af67886586ff2477c53828f104d6f2a.png)**

**[https://blockchair.com/bitcoin/transaction/2a058e289f96000016d48ceae16bc798ceec3fad2594c4c07fbe935e38e32688](https://blockchair.com/bitcoin/transaction/2a058e289f96000016d48ceae16bc798ceec3fad2594c4c07fbe935e38e32688?_type=transaction&_search=homepage#o=2)**

**如果你在[https://blockchair.com/](https://blockchair.com/)中搜索单词，你实际上可以看到一堆这样的信息:**

**![](img/847d101bc29549dc53661666a9c02f18.png)**

**如果你想了解更多关于如何创建这类交易的信息，请查看这篇文章。**

**另一个在区块链留下你印记的小方法是使用虚荣心地址。**

****CoinJoins 和 PayJoins****

**对 Coinjoins 和 Payjoins 的深入解释真的需要一篇自己的文章(我可能有一天会这样做)，所以我将不得不大大简化这一部分，省略许多重要的细节。目前，如果你想了解更多关于 Coinjoins 和 Payjoins 的知识，我推荐你看看这些文章:
[【https://en.bitcoin.it/wiki/CoinJoin】](https://en.bitcoin.it/wiki/CoinJoin)

[https://joinmarket.me/blog/blog/payjoin/](https://joinmarket.me/blog/blog/payjoin/)**

**简而言之，Coinjoin 是一个简单的事务，旨在通过将事务的输入和输出归属于多个实体来打破公共输入所有权启发式规则。如果区块链分析师无法推断出哪些投入和产出属于哪些实体，他们的调查实际上是没有意义的。Coinjoin 事务通常如下所示:**

**![](img/87fee256bd462fc898c1d14396cea03b.png)**

**Coinjoin 事务通常具有属于许多不同实体的大量输入和输出，并且输出的值几乎相同(值相同以避免数量相关性启发式)。然而，这并不是他们唯一可以看的方式，以下也是一个 CoinJoin:**

**![](img/a2a8354d0b9188243c291d2a4cc82ae1.png)**

**[https://blockchair.com/bitcoin/transaction/323df21f0b0756f98336437aa3d2fb87e02b59f1946b714a7b09df04d429dec2](https://blockchair.com/bitcoin/transaction/323df21f0b0756f98336437aa3d2fb87e02b59f1946b714a7b09df04d429dec2)**

**目前，有三种主要的钱包提供 CoinJoin 功能:
Samourai 钱包:[https://samouraiwallet.com/](https://samouraiwallet.com/)
Wasabi 钱包:[https://wasabiwallet.io/](https://wasabiwallet.io/)
join market:[https://github.com/JoinMarket-Org/joinmarket-clientserver](https://github.com/JoinMarket-Org/joinmarket-clientserver)**

**由这些钱包中的每一个创建的硬币连接具有一些独特的技术特征，因此有可能识别哪个硬币连接属于哪个钱包。一般来说，识别 CoinJoin 事务不是什么大问题，但是，解决 CoinJoin 事务本身完全是另一回事。目前，只有在 CoinJoin 构造不正确(例如地址被输入和输出重复使用)或在 CoinJoin 事务后涉及的实体犯了隐私错误(例如，在事务中使用新的“干净的”CoinJoin 输出和旧的“不干净的”可跟踪输出)时，才可能找到输入和输出之间的联系(无论是在几个输入/输出之间还是输入与输出之间)。就连欧洲刑警组织在接下来的 2020 年报告中也承认了这一点:
[https://www . tbstat . com/WP/uploads/2020/06/Europol-Wasabi-Wallet-report . pdf](https://www.tbstat.com/wp/uploads/2020/06/Europol-Wasabi-Wallet-Report.pdf)**

**用于分析 CoinJoins 是否被正确构建以及相关实体是否犯了任何错误的绝对最好的免费工具是[https://www.kycp.org/#/](https://www.kycp.org/#/)区块链浏览器。我不会解释它是如何工作的，因为谢天谢地，这里已经有一个关于如何使用它的非常全面的指南:
[https://medium . com/samourai-wallet/known-your-coin-privacy-using-kycp-org-7b3b 4385 d8b](/samourai-wallet/knowing-your-coin-privacy-using-kycp-org-7b3b4385d8b)**

**总而言之，CoinJoin 交易仍然是比特币用户可用的最强大的隐私工具，其唯一的主要缺点是这种类型的交易很容易识别***

**PayJoin 事务部分解决了 CoinJoin 的这一缺陷。Payjoin 交易本质上是与常规 2 输出交易没有区别的支付 CoinJoin 交易，以下是 Payjoin 交易的一个示例:**

**![](img/ed7d9f0728071b966018d18eb81893e3.png)**

**[https://blockchair.com/bitcoin/transaction/14ff5e5c9206f7722b0ed421d26f1cb003c523070c4882150073817683bd2dda](https://blockchair.com/bitcoin/transaction/14ff5e5c9206f7722b0ed421d26f1cb003c523070c4882150073817683bd2dda)**

**看起来只是普通的交易，对吗？合理的假设是两个输入属于同一个所有者，第二个输出(0.004… BTC)是目的地址，而第一个输出是变化。然而，实际上，这是一笔一方付给另一方 0.002 BTC 的款项。输入 1 和输出 2 属于在该交易中获得 0.002 BTC 的商家(我们可以看到输出 2 金额等于输入 1 金额加上 0.002 BTC)，而输入 2 和输出 1 属于向商家支付 0.002 BTC 的客户(因为输出 1 等于输入 2 减去 0.002 BTC 和交易费)。这是对同一笔交易的两种截然不同的解读，肯定会让任何比特币分析师感到头疼。**

**这笔交易实际上被它的主人在推特上发布为一个有趣的“猜金额游戏”:
[https://twitter.com/BitcoinQ_A/status/1335517903127539713](https://twitter.com/BitcoinQ_A/status/1335517903127539713)**

**PayJoin 交易模式的缺点是，它只适用于支付，因此它的使用案例有限。然而，由于支付连接与正常交易难以区分，因此它们的采用对整个比特币网络非常重要，因为它们可能会在区块链的司法调查中埋下不确定性的种子。**

***这要复杂得多，但同样超出了本文的范围。**

****其他杂项识别模式****

**在这一节中，我想简要地提到其他各种各样的识别模式，这些模式太晦涩难懂或技术性太强，不值得单独列出一节，但了解它们可能会很有趣:**

**BIP 69 交易输入和输出的字典式索引—有一个提议的标准，用于为 BTC 钱包客户排序交易的输入和输出。虽然该标准旨在提高交易的隐私性，但一些钱包实现了它，而另一些则没有，这意味着它可以成为钱包指纹识别的另一个线索:
[https://en.bitcoin.it/wiki/BIP_0069](https://en.bitcoin.it/wiki/BIP_0069)
[https://transaction fee . info/charts/transactions-bip 69-compliant/](https://transactionfee.info/charts/transactions-bip69-compliant/)**

**低 R 值签名——BTC 核心钱包通过稍微缩短签名来节省交易空间。没有其他钱包这样做(至少根据[https://en.bitcoin.it/wiki/Privacy](https://en.bitcoin.it/wiki/Privacy)):
[https://github.com/bitcoin/bitcoin/pull/13666](https://github.com/bitcoin/bitcoin/pull/13666)**

**未压缩/压缩的公钥——当 Satoshi 在创造比特币的时候，他们没有意识到 BTC 公钥可以被压缩以节省空间而没有任何副作用。最终，人们意识到了这一点，钱包开始使用压缩的公钥，但仍然有一些使用未压缩的公钥，所以这使它们与众不同:
[https://bit coin . stack exchange . com/questions/3059/what-a-compressed-bit coin-key](https://bitcoin.stackexchange.com/questions/3059/what-is-a-compressed-bitcoin-key)
[https://www . ntirawen . com/2019/03/bit coin-compressed-and-uncompressed . html](https://www.ntirawen.com/2019/03/bitcoin-compressed-and-uncompressed.html)**

**硬币选择——能够选择您希望花费的特定 UTXO。一些钱包提供这种功能(如比特币核心钱包):
[https://no para 73 . medium . com/coin-control-is-must-learn-if-you-care-you-privacy-in-bit coin-33 B9 a5f 224 a 2](https://nopara73.medium.com/coin-control-is-must-learn-if-you-care-about-your-privacy-in-bitcoin-33b9a5f224a2)**

**[Sybil 攻击](https://en.wikipedia.org/wiki/Sybil_attack) —一种针对点对点系统的旧类型攻击，攻击者伪装成几个不同的实体来破坏系统的隐私或安全。在比特币中，这种攻击通常与网络取证有关，但由于 CoinJoins 是点对点系统，它们也可能受到 Sybil 攻击的威胁。Oxt.research 团队已经观察到这种攻击是无意中在 CoinJoin 钱包上执行的，因此能够跟踪他们的目标实体。如果你有兴趣了解更多，我推荐你点击这里查看他们的研究:
[https://research.oxt.me/publications](https://research.oxt.me/publications)**

**侧链——一种允许将比特币转移到另一个独立的区块链的机制。另一个区块链可能具有非常不同的隐私特征。在目前的实践中，比特币唯一有效的侧链是液态侧链。你可以在这里读到更多关于侧链的信息:
[https://en.bitcoin.it/wiki/Sidechain](https://en.bitcoin.it/wiki/Sidechain)**

**手工 coin join——coin join 交易可以手工制作，不需要特殊的钱包，只需使用原始交易 API。这提供了一定程度的灵活性:
[https://en.bitcoin.it/wiki/Raw_Transactions](https://en.bitcoin.it/wiki/Raw_Transactions)**

**污点分析——一种过于简单的区块链分析技术，分析师假设通过交易接触他们跟踪的地址(第一个“被污染”的地址)的每个地址也是“被污染的”。当调查暗网市场时，这种假设可能是有用的，但是没有考虑所有权的转移，因此它的有效性实际上是有限的。在这里阅读更多:
https://bitcoin-class.weebly.com/taint-analysis.html**

**集中式混合器——在 CoinJoins 出现之前，以托管方式混合比特币很流行。关于集中式 mixer 隐私如何工作(在许多情况下不工作)有很多变化，但是，一般来说，集中式 mixer 的隐私不会比分散式 CoinJoin 提供的更好。此外，当你使用它的时候，你必须信任你的钱。就我个人而言，我不会这样做，无论中央调音台 pinky 多么努力地承诺他们不会监视你，也不会把你的信息提供给 FBI/CIA/KGB/NSA/M16……调音台通常相当可疑，只能在暗网上找到。你可以在这里了解更多:
[https://en.bitcoin.it/wiki/Mixing_service](https://en.bitcoin.it/wiki/Mixing_service)
[https://medium . com/Cobo-vault/bit coin-mixing-a-brief-research-into-centralized-vs-decentralized-390 ea 83 fa 6 ad](/cobo-vault/bitcoin-mixing-a-brief-research-into-centralized-vs-decentralized-390ea83fa6ad)**

****隐私的未来:****

**随着新工具的引入，比特币隐私不断发生变化，这些新工具使比特币分析变得更难/更容易，与此同时，比特币网络也在整体上逐渐发生变化。虽然未来永远无法完全预测，但我们可以尽最大努力对太空将如何演变以及哪些技术将影响这一变化做出一些一般性猜测:**

**[闪电网络](https://en.bitcoinwiki.org/wiki/Lightning_Network)——随着越来越多的人(希望如此)开始使用闪电网络，它对比特币隐私的影响将越来越明显。只有闪电网络的初始和退出交易在比特币账本上可见，而初始和退出交易之间可能有数千个间歇交易都发生在链外。遗憾的是，我对闪电网络的了解还不足以确定它对比特币分析的全面影响，但它肯定会有影响。但我会留下一些文章的链接，从这些文章中你或许可以开始形成自己的观点:
[https://en.bitcoinwiki.org/wiki/Lightning_Network](https://en.bitcoinwiki.org/wiki/Lightning_Network)
[https://medium . com/suredbits/lightning-network-101-privacy-245d 98210 ab](/suredbits/lightning-network-101-privacy-245d98210ab)
[https://arxiv.org/abs/2003.12470](https://arxiv.org/abs/2003.12470)
[https://www . coin desk . com/bit coin-lightning-privacy-point-time lock-contracts-ptlcs](https://www.coindesk.com/bitcoin-lightning-privacy-point-timelock-contracts-ptlcs)**

**coin swap——join market 背后的一个人正在开发一种新的隐私工具，他是编写大规模比特币维基的[隐私页面](https://en.bitcoin.it/wiki/Privacy)的人，比特币隐私的主和救世主——克里斯·贝尔彻。Coinswap 承诺提供比特币隐私的圣杯，coinjoin 既无法识别，又方便每个人使用，不仅仅是商家和买家。更多关于 CoinSwap 协议的信息可以在这里阅读:
[https://gist . github . com/Chris-belcher/9144 BD 57 a91c 194 e 332 FB 5 ca 371d 0964](https://gist.github.com/chris-belcher/9144bd57a91c194e332fb5ca371d0964)**

**Taproot 和 on-chain 的变化——比特币协议不时发生变化，最近一次重大变化是 [Segwit softfork](https://en.wikipedia.org/wiki/SegWit) 。下一个将是直根软叉。除了其他目标之外，这个 softfork 还试图提高比特币脚本的隐私性。再一次，我很惭愧地承认我对这个 softfork 了解得不够，所以目前，我将遵从比特币 wiki 和一些很棒的文章，在那里你可以读到更多关于 Taproot 的内容:
[https://en.bitcoin.it/wiki/BIP_0341](https://en.bitcoin.it/wiki/BIP_0341)
[https://medium . com/interdax/how-will-schn orr-signatures-benefit-bit coin-b 4482 cf 85d 40](/interdax/how-will-schnorr-signatures-benefit-bitcoin-b4482cf85d40)
[https://medium . com/interdax/what-is-Taproot-and](/interdax/what-is-taproot-and-how-will-it-benefit-bitcoin-5c8944eed8da)**

**“免费区块链取证”文章的第二部分到此结束，有了这些信息，任何人都可以自行进行区块链取证调查，并取得相当大的成功。在文章的第三部分，我自己做了一个小调查来展示这是如何实现的。如果你感兴趣，可以去看看；)**

## **另外，阅读**

*   **最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)**
*   **[Uniswap API](https://bitquery.io/blog/uniswap-pool-api) —如何获取 Uniswap 数据？**
*   **[AAX 交易所评论](/coinmonks/aax-exchange-review-2021-67c5ea09330c) |推荐代码、交易费用、利弊**
*   **[Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet**
*   **[FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)**
*   **[Bybit 交换评论](/coinmonks/bybit-exchange-review-dbd570019b71)**
*   **最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)**
*   **[加密复制交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)**
*   **[bits gap vs 3 commas vs quad ency](https://blog.coincodecap.com/bitsgap-3commas-quadency)**
*   **最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)**
*   **[最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)**
*   **最佳[加密借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)**
*   **[莱杰纳米 S vs 特雷佐 one vs 特雷佐 T vs 莱杰纳米 X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)**
*   **[block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut**
*   **Bitsgap 评论——一个轻松赚钱的加密交易机器人**
*   **为专业人士设计的加密交易机器人**
*   **[PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易**
*   **[Altrady 审查](https://blog.coincodecap.com/altrady-reivew)**
*   **[埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)**
*   **[SecuX Stone 评论](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)**
*   **[BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |从您的密码中赚取高达 8.6%的利息**
*   **[面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)**
*   **[最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)**
*   **[加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱**
*   **顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商**
*   **最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)**
*   **了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)**

> **[直接在您的收件箱中获得最佳软件交易](/coinmonks/newsletters/coinmonks)**

**[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://medium.com/coinmonks/newsletters/coinmonks)**