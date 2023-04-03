# 在 Haskell 中创建区块链

> 原文：<https://medium.com/coinmonks/create-blockchain-in-haskell-e65df30878bc?source=collection_archive---------4----------------------->

## 在 Haskell 中滚动自己的区块链

![](img/2e8d66adc0bc6e5a7cd0f7964db492d7.png)

[source](https://koukia.ca/create-a-private-ethereum-consorium-blockchain-in-azure-3667185276b5)

> ***归属*** *—这是* [*迈克尔·伯格的*](https://twitter.com/TheMichaelBurge) *博文* [在 Haskell 中滚动自己的区块链](https://www.michaelburge.us/2017/08/17/rolling-your-own-blockchain.html)的转载作品

[![](img/8eb99069c498a3c388e6b629c28eb400.png)](https://cryptofi.co/)

**Create Crypto Invoices**

[**Cryptofi —创建加密发票，跟踪您的加密支付**](https://cryptofi.co/)

[**使用 Coinmonks 作业门户**](https://coinmonks.com/) 查找加密作业

[***加入 Coinmonks 投稿人电报组。***](/coinmonks/coinnmonks-crypto-writers-telegram-group-f56b4621af0a)

[***捐投投僧***](/coinmonks/monks-need-your-help-7440418d67ec)

[![](img/24325228c537a09e28d6e4d8a7d100b8.png)](https://coinmonks.com)

**Click to find blockchain Jobs**

比特币和以太坊提供了一种去中心化的处理货币、合同和所有权令牌的方式。从技术的角度来看，它们有许多可移动的部分，并提供了演示编程语言的好方法。

本文将开发一个简单的类似区块链的数据结构，用 Haskell 来演示这些:

*   编写二进制序列化程序和反序列化程序
*   使用加密原语计算哈希
*   根据计算时间自动调整挖掘器的难度。

我们将把它命名为 Haskoin。请注意，在未来的文章之前，它不会有任何网络或钱包安全。

# 什么是区块链？

编写任何软件应用程序的第一步总是要弄清楚你的数据结构。无论是 Haskell、Perl、C 还是 SQL 都是如此。我们将把主要类型和 typeclass 实例放在它们自己的模块中:

```
**{-# LANGUAGE GeneralizedNewtypeDeriving, NoImplicitPrelude, DeriveTraversable, DeriveDataTypeable, StandaloneDeriving, TypeSynonymInstances, FlexibleInstances #-}**

**module** Haskoin.Types **where**

**import** Protolude
**import** Crypto.Hash

**import** Control.Comonad.Cofree
**import** Data.Data
**import** **qualified** Data.Vector **as** V

**newtype** **Account** **=** **Account** **Integer** **deriving** (**Eq**, **Show**, **Num**)

**data** **Transaction** **=** **Transaction** {
  _from   **::** **Account**,
  _to     **::** **Account**,
  _amount **::** **Integer**
  } **deriving** (**Eq**, **Show**)

**newtype** **BlockF** a **=** **Block** (**V.Vector** a) **deriving** (**Eq**, **Show**, **Foldable**, **Traversable**, **Functor**, **Monoid**)
**type** **Block** **=** **BlockF** **Transaction**

**type** **HaskoinHash** **=** **Digest** **SHA1**

**data** **BlockHeader** **=** **BlockHeader** {
  _miner       **::** **Account**,
  _parentHash  **::** **HaskoinHash**
  } **deriving** (**Eq**, **Show**)

**data** **MerkleF** a **=** **Genesis**
               **|** **Node** **BlockHeader** a
               **deriving** (**Eq**, **Show**, **Functor**, **Traversable**, **Foldable**)

**type** **Blockchain** **=** **Cofree** **MerkleF** **Block**
```

`MerkleF`是一种高阶 Merkle 树类型，它将一个层添加到其他类型上。`Cofree MerkleF Block`做了两件事:它递归地应用`MerkleF`为 Merkle 树的所有深度生成一个类型，并为树中的每个节点附加一个`Block`类型的注释。

当使用`Cofree`时，`anno :< xf`将构造这些注释值中的一个。

查看每个节点都知道其父节点的“倒置”树比查看每个节点都知道其子节点的树更有用。如果每个节点都知道它的子节点，那么在末尾添加一个新块就需要改变树中的每个节点。所以`MerkleF`产生的是一条链，而不是一棵树。

`[Protolude](https://hackage.haskell.org/package/protolude)`是我最近在中等规模的项目中使用的`Prelude`的替代品。`Prelude`有很多向后兼容的问题，所以很多人用`NoImplicitPrelude`语言扩展关闭它，并导入一个自定义的。

为什么我们选择这个奇怪的`MerkleF`类型而不是下面这个简单的？

```
**newtype** **Block** **=** **Block** (**V.Vector** **Transaction**)
**data** **Blockchain** **=** **Genesis** **Block**
                **|** **Node** **Block** **BlockHeader** **Blockchain**
```

主要原因是获得那些`Functor`、`Traversable`和`Foldable`实例，因为我们可以使用它们来处理 Merkle 树，而无需编写任何代码。例如，给定一个区块链

```
**import** **qualified** Data.Vector **as** V

**let** genesis_block **=** **Block** (**V.**fromList **[]**)
**let** block1 **=** **Block** (**V.**fromList [**Transaction** 0 1 1000])
**let** genesis_chain **=** genesis_block **:<** **Genesis**
**let** chain1 **=** block1 **:<** **Node** (**BlockHeader** { _miner **=** 0, _parentHash **=** undefined }) genesis_chain
**let** chain2 **=** block1 **:<** **Node** (**BlockHeader** { _miner **=** 0, _parentHash **=** undefined }) chain1
```

，您可以通过以下方式获得它的所有事务:

```
**let** txns **=** toList **$** mconcat **$** toList chain2
*-- [Transaction {_from = Account 0, _to = Account 1, _amount = 1000},Transaction {_from = Account 0, _to = Account 1, _amount = 1000}]*
**let** totalVolume **=** sum **$** map _amount txns
*-- 2000*
```

我使用`stack ghci`输入一个交互式提示来测试上面的内容。

Real 区块链在报头中有很多有用的东西，比如时间戳或者 nonce 值。我们可以在需要时将它们添加到`BlockHeader`中。

# 构建链

一堆难以使用的抽象类型本身并不是很有用。我们需要一种方法来挖掘新的区块来做任何有趣的事情。换句话说，我们想要定义`mineOn`和`makeGenesis`:

```
**module** Haskoin.Mining **where**

**type** **TransactionPool** **=** **IO** [**Transaction**]

mineOn **::** **TransactionPool** **->** **Account** **->** **Blockchain** **->** **IO** **Blockchain**
mineOn pendingTransactions minerAccount root **=** undefined

makeGenesis **::** **IO** **Blockchain**
makeGenesis **=** undefined
```

genesis 块非常简单，因为它没有头:

`makeGenesis **=** return **$** **Block** (**V.**fromList **[]**) **:<** **Genesis**`

如果我们知道如何计算父节点的散列，我们就可以很容易地编写`mineOn`,没有任何困难、事务限制或安全性:

```
mineOn **::** **TransactionPool** **->** **Account** **->** **Blockchain** **->** **IO** **Blockchain**
mineOn pendingTransactions minerAccount parent **=** **do**
  ts **<-** pendingTransactions
  **let** block **=** **Block** (**V.**fromList ts)
  **let** header **=** **BlockHeader** {
        _miner **=** minerAccount,
        _parentHash **=** hash parent
        }
  return **$** block **:<** **Node** header parent

hash **::** **Blockchain** **->** **HaskoinHash**
hash **=** undefined
```

`Crypto.Hash`有很多方法来散列一些东西，我们之前已经选择了`type HaskoinHash = Digest SHA1`。但是为了使用它，我们需要一些实际的字节来散列。这意味着我们需要一种方法来序列化和反序列化一个`Blockchain`。一个常用的库是`[binary](https://hackage.haskell.org/package/binary)`，它提供了一个`Binary`类型类，我们将为我们的类型实现它。

手工写实例并不难，但是使用怪异递归类型的一个好处是编译器可以为我们生成`Binary`实例。下面是序列化和反序列化我们需要的每种类型的完整代码:

```
**{-# LANGUAGE StandaloneDeriving, TypeSynonymInstances, FlexibleInstances, UndecidableInstances, DeriveGeneric, GeneralizedNewtypeDeriving #-}**

**module** Haskoin.Serialization **where**

**import** Haskoin.Types
**import** Control.Comonad.Cofree
**import** Crypto.Hash
**import** Data.Binary
**import** Data.Binary.Get
**import** Data.ByteArray
**import** **qualified** Data.ByteString **as** BS
**import** **qualified** Data.ByteString.Lazy **as** BSL
**import** Data.Vector.Binary
**import** GHC.Generics

**instance** (**Binary** (f (**Cofree** f a)), **Binary** a) **=>** **Binary** (**Cofree** f a) **where**
**instance** (**Binary** a) **=>** **Binary** (**MerkleF** a) **where**
**instance** **Binary** **BlockHeader** **where**
**instance** **Binary** **Transaction** **where**
**deriving** **instance** **Binary** **Account**
**deriving** **instance** **Binary** **Block**

**deriving** **instance** **Generic** (**Cofree** f a)
**deriving** **instance** **Generic** (**MerkleF** a)
**deriving** **instance** **Generic** **BlockHeader**
**deriving** **instance** **Generic** **Transaction**
**instance** **Binary** **HaskoinHash** **where**
  get **=** **do**
    mDigest **<-** digestFromByteString **<$>** (get **::** **Get** **BS.ByteString**)
    **case** mDigest **of**
      **Nothing** **->** fail "Not a valid digest"
      **Just** digest **->** return digest
  put digest **=** put **$** (convert digest **::** **BS.ByteString**)

deserialize **::** **BSL.ByteString** **->** **Blockchain**
deserialize **=** decode

serialize **::** **Blockchain** **->** **BSL.ByteString**
serialize **=** encode
```

我只包括了`deserialize`和`serialize`以使这个模块的最终结果更加清晰。让我们放弃他们，支持`Data.Binary`中的`decode`和`encode`。

`Generic`是一种将值转换成非常轻量级的“语法树”的方法，可以被序列化器(JSON、XML、Binary 等)使用。)和许多其他 typeclasses 来提供有用的默认定义。Haskell wiki 有一个很好的概述。`binary`使用这些`Generic`实例来定义可以处理任何事情的序列化器。

我们不得不为`HaskoinHash`手工编写一个`Binary`实例，因为`Crypto.Hash`库中的`Digest SHA1`没有提供它或`Generic`实例。没关系——无论如何，摘要基本上都是字节串，所以只有几行。

下面是如何使用它们来实现`mineOn`:

```
**import** Crypto.Hash(hashlazy)

mineOn **::** **TransactionPool** **->** **Account** **->** **Blockchain** **->** **IO** **Blockchain**
mineOn pendingTransactions minerAccount parent **=** **do**
  ts **<-** pendingTransactions
  **let** block **=** **Block** (**V.**fromList ts)
  **let** header **=** **BlockHeader** {
        _miner **=** minerAccount,
        _parentHash **=** hashlazy **$** encode parent
        }
  return **$** block **:<** **Node** header parent
```

下面是如何测试这是否真的有效:

```
testMining **::** **IO** **Blockchain**
testMining **=** **do**
  **let** txnPool **=** return **[]**
  chain **<-** makeGenesis
  chain **<-** mineOn txnPool 0 chain
  chain **<-** mineOn txnPool 0 chain
  chain **<-** mineOn txnPool 0 chain
  chain **<-** mineOn txnPool 0 chain
  chain **<-** mineOn txnPool 0 chain
  return chain

*-- GHCI*
**>** chain **<-** testMining
**Block** **[]** **:<** **Node** (**BlockHeader** {_miner **=** **Account** 0, _parentHash **=** efb3febc87c41fffb673a81ed14a6fb4f736df79}) (
  **Block** **[]** **:<** **Node** (**BlockHeader** {_miner **=** **Account** 0, _parentHash **=** 2accb557297850656de70bfc3e13ea92a4ddac29}) (
    **Block** **[]** **:<** **Node** (**BlockHeader** {_miner **=** **Account** 0, _parentHash **=** f51e30233feb41a228706d1357892d16af69c03b}) (
      **Block** **[]** **:<** **Node** (**BlockHeader** {_miner **=** **Account** 0, _parentHash **=** 0072e83ae8e9e22d5711fd832d350f5a279c1c12}) (
        **Block** **[]** **:<** **Node** (**BlockHeader** {_miner **=** **Account** 0, _parentHash **=** c259e771b237769cb6bce9a5ab734c576a6da3e1}) (
          **Block** **[]** **:<** **Genesis**)))))
**>** encode chain
"\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\SOH\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\DC4\239\179\254\188\135\196\US\255\182s\168\RS\209Jo\180\247\&6\223y\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\SOH\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\DC4*\204\181W)xPem\231\v\252>\DC3\234\146\164\221\172)\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\SOH\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\DC4\245\RS0#?\235A\162(pm\DC3W\137-\SYN\175i\192;\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\SOH\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\DC4\NULr\232:\232\233\226-W\DC1\253\131-5\SIZ'\156\FS\DC2\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\SOH\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\DC4\194Y\231q\178\&7v\156\182\188\233\165\171sLWjm\163\225\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL\NUL"
**>** (decode **$** encode chain) **::** **Blockchain**
**Block** **[]** **:<** **Node** (**BlockHeader** {_miner **=** **Account** 0, _parentHash **=** efb3febc87c41fffb673a81ed14a6fb4f736df79}) (
  **Block** **[]** **:<** **Node** (**BlockHeader** {_miner **=** **Account** 0, _parentHash **=** 2accb557297850656de70bfc3e13ea92a4ddac29}) (
    **Block** **[]** **:<** **Node** (**BlockHeader** {_miner **=** **Account** 0, _parentHash **=** f51e30233feb41a228706d1357892d16af69c03b}) (
      **Block** **[]** **:<** **Node** (**BlockHeader** {_miner **=** **Account** 0, _parentHash **=** 0072e83ae8e9e22d5711fd832d350f5a279c1c12}) (
        **Block** **[]** **:<** **Node** (**BlockHeader** {_miner **=** **Account** 0, _parentHash **=** c259e771b237769cb6bce9a5ab734c576a6da3e1}) (
          **Block** **[]** **:<** **Genesis**)))))
```

如果你在家测试序列化代码，你可能更喜欢使用`base16-bytestring`库来十六进制编码你的`ByteString`:

```
**>** **import** **qualified** Data.ByteString.Base16.Lazy **as** BSL
**>** chain **<-** testMining
**>** **BSL.**encode **$** encode chain
00000000000000000100000000000000000000000014efb3febc87c41fffb673a81ed14a6fb4f736df79000000000000000001000000000000000000000000142accb557297850656de70bfc3e13ea92a4ddac2900000000000000000100000000000000000000000014f51e30233feb41a228706d1357892d16af69c03b000000000000000001000000000000000000000000140072e83ae8e9e22d5711fd832d350f5a279c1c1200000000000000000100000000000000000000000014c259e771b237769cb6bce9a5ab734c576a6da3e1000000000000000000
```

请注意，对于试图理解我们的序列化/反序列化代码的 C 程序员来说，这可能是一场噩梦，因为字节争论隐藏在许多真正通用的代码中。如果你想产生一个规范供人们使用(总是一个好主意)，你可能会想手工滚动你的序列化代码，使其自文档化。

# 采矿

这个所谓的区块链有几个与采矿相关的问题:

1.  人们可以有负结余，所以人们可以创建一个“替罪羊账户”,他们可以从中转移无限量的资金。
2.  没有事务限制，所以有人可能会创建一个巨大的块，让我们的矿工耗尽内存。
3.  我们总是开采空的街区，所以没人能转移钱。
4.  没有困难，所以矿工们没有证明他们做过任何工作。

我说这些都是挖掘问题，因为矿工运行的代码会处理它们。

#3 我们就等**联网**解决。剩下的我们现在可以做。

为了解决第一个问题，我们需要任何有交易的人的账户余额。让我们继续计算所有可能的账户余额:

```
blockReward **=** 1000

balances **::** **Blockchain** **->** **M.Map** **Account** **Integer**
balances bc **=**
  **let** txns **=** toList **$** mconcat **$** toList bc
      debits **=** map (**\Transaction**{ _from **=** acc, _amount **=** amount} **->** (acc, **-**amount)) txns
      credits **=** map (**\Transaction**{ _to **=** acc, _amount **=** amount} **->** (acc, amount)) txns
      minings **=** map (**\**h **->** (_minerAccount h, blockReward)) **$** headers bc
  **in** **M.**fromListWith (**+**) **$** debits **++** credits **++** minings
```

一旦我们有了父区块链，我们就知道如何过滤掉无效的事务:

```
validTransactions **::** **Blockchain** **->** [**Transaction**] **->** [**Transaction**]
validTransactions bc txns **=**
  **let** accounts **=** balances bc
      validTxn txn **=** **case** **M.**lookup (_from txn) accounts **of**
        **Nothing** **->** **False**
        **Just** balance **->** balance **>=** _amount txn
  **in** filter validTxn txns
```

为了解决#2，我将让当前矿工选择他想要放入他的块中的交易。这意味着我将在顶部放置一个常量`globalTransactionLimit = 1000`，我们将在挖掘时使用它，但我们不会使用它来验证过去的块。

为了解决第 4 个问题，我们需要在我们的`BlockHeader`中添加一个 nonce 字段，挖掘器可以增加这个字段，直到找到一个好的 hash。我们将使它成为一个任意大的整数，以避免没有 nonce 值产生足够困难的散列的情况。因为我们想调整我们的难度，所以块需要大致相同的时间来挖掘，我们将在头中存储一个时间戳。

```
**import** Data.Time.Clock.POSIX

*-- Add new fields*
**data** **BlockHeader** **=** **BlockHeader** {
  _miner       **::** **Account**,
  _parentHash  **::** **HaskoinHash**,
  _nonce       **::** **Integer**,
  _minedAt     **::** **POSIXTime**
  } **deriving** (**Eq**, **Show**)

*-- Add serializers for POSIXTime*
**instance** **Binary** **POSIXTime** **where**
  get **=** fromInteger **<$>** (get **::** **Get** **Integer**)
  put x **=** put **$** (round x **::** **Integer**)

globalTransactionLimit **=** 1000

mineOn **::** **TransactionPool** **->** **Account** **->** **Blockchain** **->** **IO** **Blockchain**
mineOn pendingTransactions minerAccount parent **=** **do**
  ts **<-** pendingTransactions
  ts **<-** return **$** validTransactions parent ts
  ts **<-** return **$** take globalTransactionLimit ts
  loop ts 0
  **where**
    validChain bc **=** difficulty bc **<** desiredDifficulty parent
    loop ts nonce **=** **do**
      now **<-** getPOSIXTime
      **let** header **=** **BlockHeader** {
            _miner **=** minerAccount,
            _parentHash **=** hashlazy **$** encode parent,
            _nonce **=** nonce,
            _minedAt **=** now
            }
          block **=** **Block** (**V.**fromList ts)
          candidate **=** block **:<** **Node** header parent
      **if** validChain candidate
        **then** return candidate
        **else** loop ts (nonce**+**1)

difficulty **::** **Blockchain** **->** **Integer**
difficulty **=** undefined

desiredDifficulty **::** **BlockChain** **->** **Integer**
desiredDifficulty **=** undefined
```

我们输入`loop`,不断增加计数器的值并获取时间，直到我们找到一个难度合适的候选人。一个`Blockchain`的实际困难只是它的 hash 转换成一个整数:

```
**import** Crypto.Number.Serialize(os2ip)

difficulty **::** **Blockchain** **->** **Integer**
difficulty bc **=** os2ip **$** (hashlazy **$** encode bc **::** **HaskoinHash**)
```

我们怎么知道什么是正确的困难？首先，我们将计算最后 100 个数据块的平均数据块间隔时间:

```
numBlocksToCalculateDifficulty **=** 100

blockTimeAverage **::** **BlockChain** **->** **NominalDiffTime**
blockTimeAverage bc **=** average **$** zipWith (**-**) times (tail times)
  **where**
    times **=** take numBlocksToCalculateDifficulty **$** map _minedAt **$** headers bc

headers **::** **BlockChain** **->** [**BlockHeader**]
headers **Genesis** **=** **[]**
headers (**_** **:<** **Node** x next) **=** x **:** headers next

average **::** (**Foldable** f, **Num** a, **Fractional** a, **Eq** a) **=>** f a **->** a
average xs **=** sum xs **/** (**if** d **==** 0 **then** 1 **else** d) **where** d **=** fromIntegral **$** length xs
```

让我们设定 10 秒的目标时间。假设`blockTimeAverage bc`给出 2 秒，那么我们希望块的时间是 5 倍:`adjustmentFactor = targetTime / blockTimeAverage bc` = 5。这意味着我们只想接受最初接受的块中的`1/5`。

由于哈希是均匀分布的，所以原来哈希的`1/5`比`originalDifficulty / 5`少，这将是我们新的难点。[比特币就是这么干的](https://bitcoin.stackexchange.com/questions/855/what-keeps-the-average-block-time-at-10-minutes) : `difficulty = oldDifficulty * (2 weeks) / (time for past 2015 blocks)`。

```
genesisBlockDifficulty **=** 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
targetTime **=** 10

*-- BEWARE: O(n * k), where k = numBlocksToCalculateDifficulty*
desiredDifficulty **::** **Blockchain** **->** **Integer**
desiredDifficulty x **=** round **$** loop x
  **where**
    loop (**_** **:<** **Genesis**) **=** genesisBlockDifficulty
    loop x**@**(**_** **:<** **Node** **_** xs) **=** oldDifficulty **/** adjustmentFactor
      **where**
        oldDifficulty **=** loop xs
        adjustmentFactor **=** min 4.0 **$** targetTime `safeDiv` blockTimeAverage x
```

以下是使用这些计算得出的一些最近的开采时间:

```
**>** exampleChain **<-** testMining
**>** exampleChain **<-** mineOn (return **[]**) 0 exampleChain *-- Repeat a bunch of times*
**>** mapM_ print **$** map blockTimeAverage **$** chains exampleChain
6.61261425s
6.73220925s
7.97893375s
12.96145975s
10.923974s
9.59857375s
7.1819445s
2.2767425s
3.2307515s
7.215131s
15.98277575s
```

他们在 10s 左右徘徊是因为`targetTime = 10`。

# 坚持

我们将把区块链保存在磁盘上，并给人们 3 个工具:

*   挖掘区块和创建新链的工具
*   列出账户余额的工具

第一个工具是矿工:

```
**{-# LANGUAGE NoImplicitPrelude, OverloadedStrings #-}**

**module** Haskoin.Cli.Mine **where**

**import** Haskoin.Mining
**import** Haskoin.Serialization
**import** Haskoin.Types

**import** Protolude
**import** System.Environment
**import** Data.Binary
**import** **qualified** Data.ByteString.Lazy **as** BSL
**import** System.Directory
**import** Prelude(read)

defaultChainFile **=** "main.chain"
defaultAccount **=** "10"

main **::** **IO** ()
main **=** **do**
  args **<-** getArgs
  **let** (filename, accountS) **=** **case** args **of**
        **[]** **->** (defaultChainFile, defaultAccount)
        [filename] **->** (filename, defaultAccount)
        [filename, account] **->** (filename, account)
        **_** **->** panic "Usage: mine [filename] [account]"
      swapFile **=** filename **++** ".tmp"
      txnPool **=** return **[]**
      account **=** **Account** **$** read accountS
  forever **$** **do**
    chain **<-** loadOrCreate filename makeGenesis **::** **IO** **Blockchain**
    newChain **<-** mineOn txnPool account chain
    encodeFile swapFile newChain
    copyFile swapFile filename
    print "Block mined and saved!"

loadOrCreate **::** **Binary** a **=>** **FilePath** **->** (**IO** a) **->** **IO** a
loadOrCreate filename init **=** **do**
  exists **<-** doesFileExist filename
  **if** exists
    **then** decodeFile filename
    **else** **do**
      x **<-** init
      encodeFile filename x
      return x
```

第二个打印所有的账户余额

```
**{-# LANGUAGE NoImplicitPrelude, OverloadedStrings #-}**

**module** Haskoin.Cli.ListBalances **where**

**import** Haskoin.Mining
**import** Haskoin.Serialization
**import** Haskoin.Types

**import** Protolude
**import** System.Environment
**import** Data.Binary
**import** **qualified** Data.Map **as** M
**import** **qualified** Data.ByteString.Lazy **as** BSL

defaultChainFile **=** "main.chain"

main **::** **IO** ()
main **=** **do**
  args **<-** getArgs
  **let** (filename) **=** **case** args **of**
        **[]** **->** (defaultChainFile)
        [filename] **->** (filename)
        **_** **->** panic "Usage: list-balances [filename]"
  chain **<-** decodeFile filename **::** **IO** **Blockchain**
  forM_ (**M.**toAscList **$** balances chain) **$** **\**(account, balance) **->** **do**
    print (account, balance)
```

这是它的输出:

```
**$** stack exec list**-**balances
(**Account** 10,23000)
```

显然，我已经开采了 23 个区块来测试`stack exec mine`。

# 结论

我们开发了一个简单的区块链数据结构。你可以在 [Github](https://github.com/MichaelBurge/haskoin) 上浏览资源库。

未来与 Haskoin 相关的文章可能涵盖

*   使用网络和并发原语建立对等网络。
*   保护钱包中的帐户，这样其他人就无法从您的帐户中转出资金。
*   建立一个“区块链探险家”网站
*   GPU-加速我们的散列
*   FPGA-加速我们的哈希运算

未来的加密货币相关文章可能涵盖:

*   您可能听说过工作证明和利益证明。那么证据证明呢——矿工们竞相用适当的逻辑证明新的定理？
*   添加图灵完全脚本语言
*   解析命令行选项的更好方法
*   建设比特币交易所