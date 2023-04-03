# Go 中的 Schnorr 签名

> 原文：<https://medium.com/coinmonks/schnorr-signatures-in-go-80a7fbfe0fe4?source=collection_archive---------1----------------------->

我认为，当我进入密码学和一些时髦的素数曲线循环数论的混乱状态，并为我正在工作的一家新公司学习 Golang 时，我还不如尝试在 Go 中介绍 Schnorr 签名方案。

让我们使用基于离散对数问题的非对称密码系统(任何处理椭圆曲线的 ole 系统都可以)。

为了简化处理椭圆曲线的操作，我们将利用一个方便的库 [dedis/kyber](https://github.com/dedis/kyber) 。

以下是我们一如既往的进口商品:

```
**import** (
   **"gopkg.in/dedis/kyber.v2"
   "gopkg.in/dedis/kyber.v2/group/edwards25519"
   "fmt"** )
```

为了表明`kyber`作为一个库有多好，我们将椭圆曲线的基点定义为生成器`G`。通过选择一个随机标量作为我们的私钥`x`，我们可以得到我们的公钥`y`，也就是`y = x * G`。

换句话说，我们在我们的椭圆曲线的加法群中执行运算`x`次。

以上所有内容都可以用下面的代码来表示:

```
privateKey := curve.Scalar().Pick(curve.RandomStream())
publicKey := curve.Point().Mul(privateKey, curve.Point().Base())

fmt.Printf(**"Generated private key: %s\n"**, privateKey)
fmt.Printf(**"Derived public key: %s\n\n"**, publicKey)
```

正如您可能已经从曲线中猜到的，我们将使用椭圆曲线组 Edwards 25519，这是另一个签名方案变体 EdDSA 广泛使用的。

我们将使用 SHA256 作为散列函数，它将对要签名的消息进行指纹识别。

我们还将定义一个漂亮的`struct`来保存一些数据集，这些数据集将代表我们的消息签名和我们的纯文本散列函数

```
**var** curve = edwards25519.NewBlakeSHA256Ed25519()
**var** sha256 = curve.Hash()

**type** Signature **struct** {
   r kyber.Point
   s kyber.Scalar
}**func** Hash(s string) kyber.Scalar {
   sha256.Reset()
   sha256.Write([]byte(s))

   **return** curve.Scalar().SetBytes(sha256.Sum(nil))
}
```

所以，让我们深入研究一下这个方案。

围绕离散对数方案，我们将利用陷门函数，它基本上代表一个反函数极难计算的函数。

值得庆幸的是，由于我们选择的 hash(sha 256)具有非常好的抗碰撞攻击的特性，它非常适合如下的 Schnorr 方案。

我们定义一个签名验证函数，其中:

`H'(m, s, e) = H(m || s * G + e * y)`
…定义为在`H'(m, s, e) = e`时已经验证了某组数据。

`m`是被签名的消息，`s`是我们签名的第 1 部分，`e`是我们的消息与变量`r`连接的散列，变量`r`是我们签名的第 2 部分。

`m, r, s, e, G, y`被公开分发，以供用户验证由`x, y`定义的非对称密钥对下的某个用户确实签署了给定的消息`m`。

为了证明只有拥有这样一个密钥对的用户(或受到攻击的用户)才有可能为消息`m`创建签名部分`r, s`使得`H’(m, s, e) = e`，回想一下`y = x * G`:

```
H'(m, s, e) = e = H(m || s * G + e * y)
e = H(m || s * G + e * y)
e = H(m || s * G + e * x * G)
e = H(m || G(s + e * x))
```

由于`s, e`和`m`是已知的，使条件语句`e == e`为真的唯一方法是用户知道私钥`x`。Q.E.D…？

为了对消息`m`进行签名并导出签名部分`r`和`s`，我们选择一个随机标量`k`作为我们签名方案的种子。

我们在曲线的乘法群中围绕我们的生成点`G`运算`k`次以导出`r = k * G`。`k`是我们定义的使整个方案成功/失败的剩余和，我们定义为:

`k = s + e * x`；于是`H'(m, s, e) = H(m || k * G) = H(m || r) = e`。

因此，在导出`r`之后，得到`e = H(m || r)`并导出:

```
s = k - e * x
```

你已经准备好你的签名了！

在围棋中，你应该这样表达它:

```
*// m: Message
// x: Private key* **func** Sign(m string, x kyber.Scalar) Signature {
   *// Get the base of the curve.* g := curve.Point().Base()

   *// Pick a random k from allowed set.* k := curve.Scalar().Pick(curve.RandomStream())

   *// r = k * G (a.k.a the same operation as r = g^k)* r := curve.Point().Mul(k, g)

   *// Hash(m || r)* e := Hash(m + r.String())

   *// s = k - e * x* s := curve.Scalar().Sub(k, curve.Scalar().Mul(e, x))

   **return** Signature{r: r, s: s}
}
```

然而，请注意，每次我们签署消息时，我们都应该选择一个全新的真正随机信息源的`k`(考虑到`H(x)`独特的前图像属性，你甚至可以用`H(m || x)`来播种它！).

有了`k = s + e * x`，让我们为两个不同的消息`(r0, s0, e0)`和`(r1, s1, e1)`获得一组签名。

**注意第`i`个索引上的`r`是从第`i`个索引上的`e`和`s`通过`r = s * G + e * y`派生出来的。

如果两条消息的`k`相同，表示:

```
k = s0 + e0 * x = s1 + e1 * x
s0 + e0 * x = s1 + e1 * x
(s0 - s1) = (e1 - e0) * x
(s0 - s1) / (e1 - e) = x
```

…因此，如果您不小心，使用固定的`k`，您可能会泄露用户的私钥`x`！

给定签名组件，您可以从这个等式中得出一个很酷的东西，消息也是用户的公钥。

这可能是通过从签名中导出公钥来验证签名的一种方法。

简单回忆一下`r = s * G + e * y`，我们可以通过下式导出`y`:

```
y = (r - s * G) * (1 / e)
```

…在围棋中可以很容易地表示为:

```
*// m: Message
// S: Signature* **func** PublicKey(m string, S Signature) kyber.Point {
   *// Create a generator.* g := curve.Point().Base()

   *// e = Hash(m || r)* e := Hash(m + S.r.String())

   *// y = (r - s * G) * (1 / e)* y := curve.Point().Sub(S.r, curve.Point().Mul(S.s, g))
   y = curve.Point().Mul(curve.Scalar().Div(curve.Scalar().One(), e), y)

   **return** y
}
```

然而，一个更好的验证签名的方法是看看我们的椭圆曲线群上的乘法和加法运算是否产生与`s * G`相同的点。

因此，这不需要我们在每轮验证中使用昂贵的散列函数计算两次。

利用某人发送给我们的签名组件`(r, s)`，以及他们的公钥和纯文本消息，我们首先获得`e = H(m || r)`，并得到:

```
r = s * G + e * y
s * G = r - e * y
```

…并检查从`r`导出的`s * G`是否等同于纯计算`s * G`。请记住，`s * G`匹配的唯一方式是使用非对称密钥对`(x, y)`派生的组件`(r, s)`是否真正属于并且只有特定的人知道。

否则，陷门功能将无法实现，因此该方案仍然是安全的。

现在，在围棋中…

```
*// m: Message
// s: Signature
// y: Public key* **func** Verify(m string, S Signature, y kyber.Point) bool {
   *// Create a generator.* g := curve.Point().Base()

   *// e = Hash(m || r)* e := Hash(m + S.r.String())

   *// Attempt to reconstruct 's * G' with a provided signature; s * G = r - e * y* sGv := curve.Point().Sub(S.r, curve.Point().Mul(e, y))

   *// Construct the actual 's * G'* sG := curve.Point().Mul(S.s, g)

   *// Equality check; ensure signature and public key outputs to s * G.* **return** sG.Equal(sGv)
}
```

…现在我们的 Schnorr 计划已经准备好了！也许更好的分布式 DAG 修剪操作。

令人惊叹的是，这完全是通过一些高中代数实现的(这受到椭圆曲线组运算的一点阻碍)。

这是我用一个很好的助手打印函数做的一个小测试案例:

```
**func** (S Signature) String() string {
   **return** fmt.Sprintf(**"(r=%s, s=%s)"**, S.r, S.s)
}**func** main() {
   privateKey := curve.Scalar().Pick(curve.RandomStream())
   publicKey := curve.Point().Mul(privateKey, curve.Point().Base())

   fmt.Printf(**"Generated private key: %s\n"**, privateKey)
   fmt.Printf(**"Derived public key: %s\n\n"**, publicKey)

   message := **"We're gonna be signing this!"** signature := Sign(message, privateKey)
   fmt.Printf(**"Signature %s\n\n"**, signature)

   derivedPublicKey := PublicKey(message, signature)
   fmt.Printf(**"Derived public key: %s\n"**, derivedPublicKey)
   fmt.Printf(**"Are the original and derived public keys the same? %t\n"**, publicKey.Equal(derivedPublicKey))
   fmt.Printf(**"Is the signature legit w.r.t the original public key? %t\n\n"**, Verify(message, signature, publicKey))

   fakePublicKey := curve.Point().Mul(curve.Scalar().Neg(curve.Scalar().One()), publicKey)
   fmt.Printf(**"Fake public key: %s\n"**, fakePublicKey)
   fmt.Printf(**"Is the signature legit w.r.t a fake public key? %t\n"**, Verify(message, signature, fakePublicKey))
}
```

现在，当涉及到在各种有趣的分散应用中利用 Schnorr 作为部分群/门限签名(环签名)的形式的应用时，它是相对容易实现的。

不过，我想把这个练习留给你。

请随意阅读这里的更多数学知识:[https://www . dead alnix . me/2017/02/17/schn orr-signatures-for-not-so-dummies/](https://www.deadalnix.me/2017/02/17/schnorr-signatures-for-not-so-dummies/)

享受:)。如果你想在 Github 上发布源代码，请告诉我。

## 另外，阅读

*   [最佳加密交易机器人](/coinmonks/whats-the-best-crypto-trading-bot-in-2020-top-8-bitcoin-trading-bot-c16adeb13317)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   [unis WAP 最佳钱包](/coinmonks/best-wallets-to-use-uniswap-e91a6385d9e8)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [顶级 DeFi 项目](/coinmonks/defi-future-10-promising-projects-in-the-defi-world-ff2b697ab006)
*   bits gap review——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [3commas Review](https://blog.coincodecap.com/3commas-review-an-excellent-crypto-trading-bot) |一款优秀的密码交易机器人
*   [3Commas vs Cryptohopper](/coinmonks/cryptohopper-vs-3commas-vs-shrimpy-a2c16095b8fe)
*   Bitmex 上的[保证金交易的白痴指南](/coinmonks/the-idiots-guide-to-margin-trading-on-bitmex-dbbd7742c6fc?source=friends_link&sk=7bfa99d2a181142510c8442c8ddb0786)
*   [加密摇摆交易权威指南](/coinmonks/the-definitive-guide-to-crypto-swing-trading-7e4af6496d4d?source=friends_link&sk=70448050bd9323b42f63bfc0bb1e60d1)
*   [Bitmex 高级保证金交易指南](/coinmonks/bitmex-advanced-margin-trading-guide-2270c195ce25?source=friends_link&sk=1d986cca731f5084b9a2db4a4bc4a7ad)
*   开发人员的最佳加密 API
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com?utm_source=coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)