# WebCrypto 的欢乐时光第 1 部分:PBKDF2

> 原文：<https://medium.com/coinmonks/fun-times-with-webcrypto-part-1-pbkdf2-815b1c978c9d?source=collection_archive---------2----------------------->

![](img/bda701d4c082c11567ccdbfe80710dd4.png)

Photo by [William Bout](https://unsplash.com/photos/RkJF2BMrLJc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/crypto?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

更新:[第二部](/p/fun-times-with-webcrypto-part-2-encrypting-decrypting-dfb9fadba5bc)出来了！

本周，我的队友——Ram Valsky 和我接到了一个非常独特的任务:使用 WebCrypto 将我们在 Node 中编写的一个加密相关模块转换为 JavaScript。因为 Ram 和我都喜欢任何与密码相关的东西，所以我们的反应如下:

![](img/b360ace06d59eaaf9ff5c1c10fe544c9.png)

Via [GIPHY](https://giphy.com/gifs/excited-screaming-jonah-hill-5GoVLqeAOo6PK)

# PBKDF2 简介

pbk df 2(Password Based Key Derivation Function，基于密码的密钥导出函数)顾名思义，是一种用于从密码中导出密钥的函数，然后可以通过加密算法将其用作加密密钥。它旨在减少加密密钥对暴力和字典攻击的脆弱性。

**什么时候用？** 

**pbk df 2 在其他哈希函数之上有什么优势？** Hash 函数被设计得很快，而 PBKDF2 却故意慢。通过增加大量的迭代和一个长的随机 salt 来实现减慢，这使得对用 PBKDF2 创建的密钥的暴力攻击更加困难。

# 导出 PBKDF2 密钥

我们的第一个任务是从一个已知的集合中导出一个 PBKDF2 密钥，该集合由哈希函数、秘密密码、salt、迭代计数和 [IV](https://en.wikipedia.org/wiki/Initialization_vector) 长度组成。

我们首先为我们的 *getDerivation* 函数创建签名:

为了使用 WebCrypto 获得一个密钥，我们需要使用`crypto.subtle`对象的`deriveBits`函数。不过有一点需要注意:`deriveBits`需要一个作为`CryptoKey`对象的秘密密码，而我们只有一个字符串😕

在浏览位于 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API) 的 WebCrypto 文档时，我们注意到了`importKey`函数。这个函数基本上返回一个 promise，这个 promise 根据它的参数解析为一个 a `CryptoKey`对象。查看输入参数列表，我们注意到一些重要的事情:密码必须以`ArrayBuffer`或`JSONWebKey`的形式给出。这就是`[TextEncoder](https://developer.mozilla.org/en-US/docs/Web/API/TextEncoder/encode)` 类发挥作用的地方。`TextEncoder`公开了`encode`函数，该函数接受一个字符串参数并返回一个`[Uint8Arra](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays/Uint8Array)y`对象，然后我们可以将其用于`importKey`函数！

For more information about the parameters of importKey check out [MDN](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/importKey)

现在我们已经将密码转换成了一个`CryptoKey`，我们可以用它来导出散列密钥。如上所述，我们将使用`deriveBits`函数来实现。在调用`deriveBits`函数之前，我们应该创建一个对象来保存它的配置:

👉请注意，就像`importKey`函数一样，`deriveBits`要求 salt 作为`ArrayBuffer`传递，而不是一个简单的字符串。

调用`deriveBits`需要三个参数:

1.  我们之前创建的参数对象
2.  我们之前导入的密钥
3.  我们希望导出的**位的数量**

😱你可能会问——为什么我们要将密钥长度乘以 8？

![](img/e07c63fe5e6066514e7658135e974260.png)

Via [Giphy](https://giphy.com/gifs/adventure-time-math-finn-the-human-9lMoyThpKynde)

如果你还记得我在文章开头提到的，我们将要解密一个在 NodeJS 中加密的对象。NodeJS 使用字节，所以我们使用的密钥长度(48)实际上是 48 字节。然而，WebCrypto 使用位。因此，为了告诉 WebCrypto 我们希望从派生中派生出 48 个字节，我们将它乘以 8，得到密钥长度的位值(48*8=384)。

# 总结一下！

WebCrypto 语法有时可能看起来很奇怪，但它实际上非常容易使用。我们发现使用位而不是字符串更容易处理，因为在这个过程中没有对它们进行转换。

在第 2 部分中，我们将描述如何使用我们导出的 PBKDF2 密钥，并将其用于 WebCrypto 的加密和解密模块。

🔥特别感谢[拉姆·瓦尔斯基](https://medium.com/u/99b08f71892e?source=post_page-----815b1c978c9d--------------------------------)和[巴拉克·阿玛尔](https://medium.com/u/174ad61b0c66?source=post_page-----815b1c978c9d--------------------------------)帮助我完成这篇文章！