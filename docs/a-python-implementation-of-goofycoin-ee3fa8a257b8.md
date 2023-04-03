# GoofyCoin 的 Python 实现

> 原文：<https://medium.com/coinmonks/a-python-implementation-of-goofycoin-ee3fa8a257b8?source=collection_archive---------3----------------------->

## 简单而无用的加密货币

![](img/785ef14f4b405ea00d134de3ea26746c.png)

Photo by [Liam Read](https://unsplash.com/@lightsonfire?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 1 —简介

GoofyCoin 大概是我们能想象到的最简单的(因此也是最没用的)加密货币。这在《比特币和加密货币技术》一书的第一章中有所描述，该书由 Arvind Narayanan、Joseph Bonneau、Edward Felten、Andrew Miller、Steven Goldfeder 撰写，由普林斯顿大学出版社于 2016 年出版。作为一名区块链初学者，出于学习目的，我尝试用 Python 实现它。我确信我的代码包含错误，也许我对书中描述的 GoofyCoin 规则的理解不是 100%准确。如果任何人发现这些问题，请在这里随意评论。

一个 jupyter 笔记本，里面有这个介绍，对 GoofyCoin 的解释(如书中所述)，还有我的代码可以在下面的 GitHub 中找到:[https://github.com/thiagoguimaraesdf/goofyCoinPython](https://github.com/thiagoguimaraesdf/goofyCoinPython)

# 2 — GoofyCoin 规则和协议

两个主要的 GoofyCoin 规则是:

```
(i) Goofy, which is a unique entity, can create new coins whenever he wants and these newly created coins belong to him.(ii) Hoever owns a GoofyCoin can transfer it on to someone else. Transferring a coin is not simply a matter of sending the coin data structure to the recipient — it’s done using cryptographic operations.
```

创建新 GoofyCoin 的协议如下所述:

```
"To create a coin, Goofy generates a unique coin ID uniqueCoinID that he’s never generated before
and constructs the string “CreateCoin [ uniqueCoinID ]”. He then computes the digital signature of
this string with his secret signing key. The string, together with Goofy’s signature, is a coin. Anyone
can verify that the coin contains Goofy’s valid signature of a CreateCoin statement, and is therefore a
valid coin."
```

转移现有硬币的协议描述如下:

```
"Let’s say Goofy wants to transfer a coin that he created to Alice. To do this he creates a new
statement that says “Pay this to Alice” where “this” is a hash pointer that references the coin in
question. And as we saw earlier, identities are really just public keys, so “Alice” refers to Alice’s public
key. Finally, Goofy signs the string representing the statement. Since Goofy is the one who originally
owned that coin, he has to sign any transaction that spends the coin. Once this data structure
representing Goofy’s transaction signed by him exists, Alice owns the coin. She can prove to anyone
that she owns the coin, because she can present the data structure with Goofy’s valid signature.
Furthermore, it points to a valid coin that was owned by Goofy. So the validity and ownership of coins
are self‐evident in the system.Once Alice owns the coin, she can spend it in turn. To do this she creates a statement that says, “Pay
this coin to Bob’s public key” where “this” is a hash pointer to the coin that was owned by her. And of
course, Alice signs this statement. Anyone, when presented with this coin, can verify that Bob is the
owner. They would follow the chain of hash pointers back to the coin’s creation and verify that at
each step, the rightful owner signed a statement that says “pay this coin to [new owner]”."
```

书中对 GoofyCoin 规则进行了更正式的总结:

```
● Goofy can create new coins by simply signing a statement that he’s making a new coin with a
unique coin ID.● Whoever owns a coin can pass it on to someone else by signing a statement that saying, “Pass
on this coin to X” (where X is specified as a public key)● Anyone can verify the validity of a coin by following the chain of hash pointers back to its
creation by Goofy, verifying all of the signatures along the way.
```

# 3 —实施代码

## 3.1 —库导入

这里使用的主要库是 ecdsa，用于生成数字签名，以及 Haslib，用于使用散列函数(SHA-3)

更多关于 ecdsa 的信息:【https://pypi.org/project/ecdsa/ 

更多关于 hashlib 的信息:【https://docs.python.org/3/library/hashlib.html 

## 3.2 —我们的随机字符串生成器

定义一个函数生成一个长度为 n 的随机字符串
***注意:*** *生成一个长度为 n 的随机字符串可能有更多的随机化算法。我在这里用了一个非常简单的方法。*

## 3.3 —创建愚蠢的密钥

我们将在 GoofyCoin 类之外创建真正的 Goofy 签名和公钥。我在这里使用 ECDSA 库(和 algo)来生成并进一步验证高飞私人签名。
有关 ECDSA algo 的更多信息:[https://www . cs . Miami . edu/home/Burt/learning/CSC 609.142/ECD sa-cert . pdf](https://www.cs.miami.edu/home/burt/learning/Csc609.142/ecdsa-cert.pdf)

## 3.4 —呆瓜类

创建 GoofyCoin 类的代码——它将使我们能够(I)创建一个硬币——仅用 Goofy 秘密签名，(ii)转移一个硬币；以及(iii)检查硬币是否有效——在下面进行了描述和很好的评论。

# 4 —测试我们的硬币

我在这里对以下情况进行测试:

> #测试 1:验证一枚全新的硬币
> 
> #测试 2:尝试转移一枚不存在的硬币
> 
> #测试 3:尝试创造一个没有愚蠢的秘密密钥的新硬币
> 
> #测试 4:验证已经被转移一次的硬币
> 
> #测试 5:验证已经被转移两次的硬币
> 
> #测试 6:验证 Max 转让的假硬币

# 5 —结束语

尽管是一枚劣质硬币，但 GoofyCoin 保证了一个中央权力机构(不幸的是在这种情况下是傻瓜)有能力创造硬币，并通过有效的验证方法转移它们。任何收到硬币的人都可以通过跟踪硬币的“路线”并检查转账是否由正确的用户进行，来检查硬币是否有效。

高飞硬币的主要问题是任何人都可以花掉自己的硬币。我们没有有效的方法来保证不良用户不会将他的硬币转移给两个或更多不同的人。这就是为什么我们叫它傻瓜硬币