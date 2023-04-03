# 使用 Javascript 生成 RSA 密钥

> 原文：<https://medium.com/coinmonks/rsa-key-generation-46500c2d6ce5?source=collection_archive---------6----------------------->

![](img/a1544359cfbd7f98353cdbde2a16f5b3.png)

Image by [Markus Spiske](https://unsplash.com/@markusspiske) from unsplash

## “从头开始学习区块链”第 1 部分

我已经开始深入了解许多安全的数据存储方法。

我发现当我实现软件时，我获得了对软件的深刻理解。因此，我将实施许多事情。名单尚未确定，但第一个是[区块链](https://en.wikipedia.org/wiki/Blockchain)。

我想我已经对区块链及其工作原理有了基本的了解。但是，我肯定无法向我妈妈解释，所以我需要更好地了解它。

为了做到这一点，我做了现在任何人都会做的事情，我谷歌了“区块链”,点击了维基百科的结果。从这里开始，我觉得我还没有完全理解的第一个术语是“密码学”。所以我点进了它的[维基百科页面](https://en.wikipedia.org/wiki/Cryptography)。

我看到密码学包含了我以前见过的东西:共享密钥和公共密钥加密。但是，我并不认为我对公钥加密有深刻的理解。当我在阅读文章的这一部分时，我看到了一些熟悉的东西:RSA。但是它是如何工作的呢？

从这里开始，我决定实现我自己的 RSA 加密系统。本文仅致力于密钥生成部分。我会在后续文章中继续我的旅程。

## 1.选择两个不同的质数 *p* 和 *q*

这不是一种产生随机素数的安全加密方法，而是一种概念验证。

## 2.计算 n = pq

## 3.计算 *λ* ( *n* )

> *λ*(*n*)=[LCM](https://en.wikipedia.org/wiki/Least_common_multiple)(*λ*(*p*)，*λ*(*q*)= LCM(*p*1，*q*1)，其中 *λ* 为[卡迈克尔的全局函数](https://en.wikipedia.org/wiki/Carmichael%27s_totient_function)

这里实际上不需要理解 Carmichael 的全功能。这是由于素数总是导致他们自己减一。我很难理解这个功能。我将感谢任何人能够向我解释拉蒙的条款。

## 4.选择一个整数 e

> 使得 1< *e*<*λ*(*n*)和 [gcd](https://en.wikipedia.org/wiki/Greatest_common_divisor) ( *e* ，*λ*(*n*)= 1；即 *e* 和 *λ* ( *n* )是[互质](https://en.wikipedia.org/wiki/Coprime)

## 5.将 *d* 确定为*d*≧e1(mod*λ*(*n*)

> *d* 是 *e* (模 *λ* ( *n* )的[模乘逆](https://en.wikipedia.org/wiki/Modular_multiplicative_inverse)。
> 
> 更明确的表述为:求解 *d* 给定*d*⋅*e*≡1(mod*λ*(*n*)。

为这一步诉诸于复制代码。我无法理解如何计算模乘逆。

## 6.密钥完成

> *公钥*由模数 *n* 和公共(或加密)指数 *e* 组成。*私钥*由私钥(或解密)指数 *d* 组成，必须保密。 *p* 、 *q* 、 *λ* ( *n* )也必须保密，因为它们可以用来计算 *d* 。

我希望本文能帮助其他人获得和我一样的理解。

从这里开始，我将使用这些密钥实现[加密](https://en.wikipedia.org/wiki/RSA_(cryptosystem)#Encryption)和[解密](https://en.wikipedia.org/wiki/RSA_(cryptosystem)#Decryption)。在这样做的时候，我也计划实施[填充](https://en.wikipedia.org/wiki/Padding_(cryptography))，很可能与 [OAEP](https://en.wikipedia.org/wiki/Optimal_asymmetric_encryption_padding) 一起。

资源:[https://en . Wikipedia . org/wiki/RSA _(密码系统)#Key_generation](https://en.wikipedia.org/wiki/RSA_(cryptosystem)#Key_generation)

参考文献:

*   [https://en . Wikipedia . org/wiki/RSA _(密码系统)#Code](https://en.wikipedia.org/wiki/RSA_(cryptosystem)#Code)
*   [https://github . com/bobvanluijt/Bitcoin-explicated/blob/master/RSA . js](https://github.com/bobvanluijt/Bitcoin-explained/blob/master/RSA.js)
*   [https://rosettacode.org/wiki/Modular_inverse#JavaScript](https://rosettacode.org/wiki/Modular_inverse#JavaScript)

如果您认为本文有帮助，请不要忘记鼓掌！

请对您的任何问题或反馈进行评论。