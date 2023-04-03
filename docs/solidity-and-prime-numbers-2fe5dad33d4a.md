# 坚实度和质数

> 原文：<https://medium.com/coinmonks/solidity-and-prime-numbers-2fe5dad33d4a?source=collection_archive---------7----------------------->

## 减少以太坊智能合约的构造者传递的参数的思想实验。

约翰·夸恩斯特罗姆

![](img/51daf083372a00ee1d5dd9de26819311.png)

假设我们正在编写一个“工厂”智能合同——它将作为发布数百个其他独特合同的蓝图。我们打算通过这些契约来管理支付偏好，因为它们将接受一个或多个 Stablecoins 作为契约的特定用例的“支付”。

**挑战:**用户有不同的偏好——有些人会接受 DAI 或 Tether，有些人会接受 TrueUSD 和 DAI，有些人会*只*接受 Basis。

我们如何通过构造函数无缝地管理所有这些参数呢？对于这个例子，让我们假设有五个不同的 Stablecoins 商家/交易者/任何人想要接受。

**选项# 1**(Lame)——在构造函数中传递五个不同的布尔参数，并将它们分配给各个布尔变量:

```
*constructor (bool _dai, bool _trueusd, bool _tether, ... ) {* *DAI_Accepted = _dai;* *TrueUSD_Accepted = _trueusd;* *Tether_Accepted = _tether;**}*
```

这种选择是可以接受的。在接受支付时，我们可以使用修饰符来检查状态变量(例如 DAI_Accepted)是否为真。

有没有更简单的方法，不需要传递 5+个参数？
*是的。*

**选项#2** (酷)—传递一个单位，它是对应于真值表的素数的倍数。

假设我们有五个不同的稳定点:
**戴****true USD****系绳****基础****避风港** (Havven？)

让我们创建一个二进制真值表，而不是传递五个布尔参数，该真值表利用 1 和 0 的序列来创建一个唯一的 **uint** ，该 uint 将存储特定智能合约的支付首选项。

这将是 5 个 1 和 0 的序列，产生 2⁵组合(32)，如下表所示。左边第一个位置代表戴和智能合约是否接受(0 =否，1 =是)。第二个位置代表 TrueUSD，依此类推。

最上面的一列代表质数，我们将乘以该质数以获得右边的最终“支付偏好”数。这个数是不是我们等式的一部分，是由对应的 0/1 决定的。

```
***DAI — TrueUSD — Tether — Basis — Haven*****3–5–7–11–13**0–0–0–0–0 = **2** … no Stablecoin accepted
0–0–0–0–1 = **13** … only Haven accepted
0–0–0–1–0 = **11** … only Basis accepted
0–0–0–1–1 = **143**
0–0–1–0–0 = **7** … only Tether accepted
0–0–1–0–1 = **91**
0–0–1–1–0 = **77**
0–0–1–1–1 = **1,001**
0–1–0–0–0 = **5** … only TrueUSD accepted
0–1–0–0–1 = **65**
0–1–0–1–0 = **55**
0–1–0–1–1 = **715**
0–1–1–0–0 = **35**
0–1–1–0–1 = **455**
0–1–1–1–0 = **385**
0–1–1–1–1 = **5,005**
1–0–0–0–0 = **3** … only DAI accepted
1–0–0–0–1 = **39**
1–0–0–1–0 = **33**
1–0–0–1–1 = **429**
1–0–1–0–0 = **21**
1–0–1–0–1 = **273**
1–0–1–1–0 = **231**
1–0–1–1–1 = **3,003**
1–1–0–0–0 = **15** … only DAI & TrueUSD accepted (my preference!)
1–1–0–0–1 = **195**
1–1–0–1–0 = **165**
1–1–0–1–1 = **2,145**
1–1–1–0–0 = **105**
1–1–1–0–1 = **1,365**
1–1–1–1–0 = **1,155**
1–1–1–1–1 = **15,015** … all stablecoins accepted
```

右边的结果数字可以在稍后使用修饰符中的模计算来引用。

让我们想象一个独特的金融合约，它接受稳定的硬币，以换取释放托管的资金(以太坊、ERC20 代币、ERC721 小饰品)。这些资金已经存在于合同中，我们假设有一个唯一的买家将接收这些资金(公共地址买家)。

```
// Contract is initialized with _paymentMethod = 15, then stored in the Payment_Preference variable. This will enable payments of either DAI or TrueUSD.constructor (uint _paymentMethod) {
    Payment_Preference = _paymentMethod;
} // A modifier to determine if payment is valid.modifier isValid(uint _mod) {
    require( (Payment_Preference % _mod) == 0);
    _;
} // A set of functions to initiate payment acceptance.function acceptDAI()     isValid(3) { ... }
function acceptTrueUSD() isValid(5) { ... }
function acceptTether()  isValid(7) { ... }
function acceptBasis()   isValid(11) { ... }
function acceptHaven()   isValid(13) { ... }
```

下一步将是模块化支付接受函数，将 accept 形式的所有函数<stablecoin>()浓缩成一个单一的模块化函数，该函数访问对应于用于除法的稳定货币和质数的键:值对结构。</stablecoin>

这是否优化了我们的汽油成本？*也许——这可能会增加它们的数量。*

这会导致我们的代码更加模块化吗？*一定。*

最终，这是我正在尝试的一种方法，以减少通过构造函数传递的参数数量。随着其他机制被引入智能合同，这可能会导致更有用的应用。

> **John quarn strom**
> www . inveth . io(暂时脱机)