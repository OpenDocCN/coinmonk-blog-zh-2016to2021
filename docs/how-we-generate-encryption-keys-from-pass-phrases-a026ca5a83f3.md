# 我们如何从通行短语中生成加密密钥

> 原文：<https://medium.com/coinmonks/how-we-generate-encryption-keys-from-pass-phrases-a026ca5a83f3?source=collection_archive---------3----------------------->

![](img/eae6a17b385e6a797d0c38d2b160d915.png)

人类喜欢文字，计算机喜欢二进制，所以我们需要找到可以生成我们可以记住的加密密钥的方法。一种方法是使用密钥导出函数(KDF)。

一种方法是 HKDF，这是一种用于密钥导出的 HMAC 方法。最初，HKDF 创建一个伪随机密钥(PRK)使用一个密码短语和一个盐值(和任何其他随机函数是…