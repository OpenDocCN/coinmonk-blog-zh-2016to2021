# 坚实的数学(第三部分:百分比和比例)

> 原文：<https://medium.com/coinmonks/math-in-solidity-part-3-percents-and-proportions-4db014e080b1?source=collection_archive---------0----------------------->

## 这篇文章是关于在 Solidity 中做数学的系列文章的第三篇。这次的题目是:**百分比和比例**。

![](img/4d3c75bf6866593cb733cc467e4f52c4.png)

# 介绍

金融数学从百分比开始。 *x* 占 *y* 的百分比是多少？ *x* 等于 *y* 的百分之多少？答案我们都知道: *x* 百分之 *y* 是*x*×*y*÷100*y*是*y*×100÷x 百分之 x，这是学校数学。

上面的公式是求解比例的特例。一般来说，比例是以下形式的方程:*a**b*=*c**d*，求解比例就是在已知其他三个的情况下求其中一个值。例如， *d* 可以从 *a* 、 *b* 和 *c* 中找到，如下:*d*=*b*×*c**a*。

在主流编程语言中简单明了，在 Solidity 中，这种简单的计算令人惊讶地具有挑战性，正如我们在[上一篇文章](/coinmonks/math-in-solidity-part-2-overflow-3cd7283714b4#8b23)中所示。原因有二:I)实性不支持分数；以及 ii)Solidity 中的数字类型可能溢出。

在 Javascript 中，可以这样简单地计算*x*×*y**z*:`x*y/z`。实际上这样的表达式不会通过安全审核，因为对于足够大的 x*和 y*的乘法运算可能会溢出，从而导致计算结果不正确。使用 SafeMath 没有太大帮助，因为它可能会使交易失败，即使最终计算结果适合 256 位。在前一篇文章中，我们将这种情况称为“幻影溢出”。像`x/z*y`或`y/z*x`那样先做除法再做乘法解决了幻影溢出问题，但可能导致精度下降。**

在这篇文章中，我们发现在 Solidity 中有什么更好的方法来处理**百分比和比例**。

# 接近全部比例

本文的目标是可靠地实现以下功能:

```
function mulDiv (uint x, uint y, uint z)
public pure returns (uint)
```

它计算*x*×*y**z*，将结果向下舍入，并在 *z* 为零或结果不符合`uint`时抛出。让我们从以下简单的解决方案开始:

```
function mulDiv (uint x, uint y, uint z)
public pure returns (uint)
{
  return x * y / z;
}
```

这个解决方案基本上满足了大部分要求:它好像是计算*x*×*y**z*，向下舍入结果，如果 *z* 为零就抛出。但是有一个问题:它实际计算的是*x*×*y***mod***2⁵⁶**z*。这就是乘法溢出在 Solidity 中的工作原理。当乘法结果不适合 256 位时，仅返回结果的最低 256 位。对于小数值的 *x* 和 *y* ，当*x*×*y*t130】2⁵⁶时，没有区别，但是对于大数值的 *x* 和 *y* 会产生不正确的结果。所以第一个问题是:

## 我们如何防止溢出？

> 剧透:我们不应该。

防止 Solidity 中乘法溢出的常见方法是使用 SafeMath 库中的`mul`函数:

```
function mulDiv (uint x, uint y, uint z)
public pure returns (uint)
{
  return mul (x, y) / z;
}
```

这段代码保证了正确的结果，所以现在所有的需求都满足了，对吗？没那么快。

需求是在结果不符合`uint`的情况下进行恢复，这个实现似乎满足了需求。然而，当 *x* × *y* 不符合`uint`时，即使最终结果符合，该实现也会恢复。我们称这种情况为“幻影溢出”。在上一篇文章中，我们展示了如何以精度为代价来解决幻影溢出，但是这个解决方案在这里不起作用，因为我们想要精确的结果。

因为仅仅恢复幻像溢出是不可行的，那么

## 我们如何避免幻影溢出保持精度？

> 剧透:简单的数学把戏。

我们来做如下替换:*x*=*a*×*z*+*b*和*y*=*c*×*z*+*d*，其中 *a* 、 *b* 、 *c* 和*d*然后:**

*x*×*y**z*=
(*a*×*z*+*b*)×(*c*×*z*+*d*)*z*=
z=
*a*×c×*z*+*a*×*d*+*b*×*c*+*b*×*d**z*

值 *a* 、 *b* 、 *c* 和 *d* 可以分别通过将 *x* 和 *y* 除以 *z* 计算为商数和提醒数。

因此，该函数可以重写如下:

```
function mulDiv (uint x, uint y, uint z)
public pure returns (uint)
{
  uint a = x / z; uint b = x % z; // x = a * z + b
  uint c = y / z; uint d = y % z; // y = c * z + d return a * b * z + a * d + b * c + b * d / z;
}
```

这里我们使用普通的`+`和`*`操作符来提高可读性，而真实的代码应该使用 SafeMath 函数来防止真实的(即非幻像的)溢出。

在这个实现中，幻像溢出仍然是可能的，但只是在最后一个术语:`b * d / z`。但是，当 *z* ≤2 ⁸时，该代码保证正确工作，因为 *b* 和 *d* 都小于 *z* ，因此 *b* × *d* 保证适合 256 位。因此，这种实现可以用于已知 *z* 不超过 2 ⁸.的情况一个常见的例子就是 18 位小数的定点乘法: *x* × *y* ÷10 ⁸.但是，

## 如何才能完全避免幻影溢出？

> 剧透:用更宽的数字。

幻像溢出问题的根源是中间乘法结果不适合 256 位。所以，让我们用更宽的字体。Solidity 本身不支持宽度超过 256 位的数值类型，所以我们必须模拟它们。我们基本上需要两个操作:*uint*×*uint*→*宽*和*宽**uint*→*uint*。

由于两个 256 位无符号整数的乘积不能超过 512 位，因此更宽的类型必须至少为 512 位宽。我们可以通过一对 256 位无符号整数来模拟 512 位无符号整数，这两个 256 位无符号整数分别保存整个 512 位数的较低和较高 256 位部分。

因此，代码可能如下所示:

```
function mulDiv (uint x, uint y, uint z)
public pure returns (uint)
{
  (uint l, uint h) = fullMul (x, y);
  return fullDiv (l, h, z);
}
```

这里`fullMul`函数将两个 256 位无符号整数相乘，并将结果作为 512 位无符号整数分成两个 256 位部分返回。函数`fullDiv`将作为两个 256 位部分传递的 512 位无符号整数除以 256 位无符号整数，并将结果作为 256 位无符号整数返回。

让我们用学校数学的方法实现这两个函数:

```
function fullMul (uint x, uint y)
public pure returns (uint l, uint h)
{
  uint xl = uint128 (x); uint xh = x >> 128;
  uint yl = uint128 (y); uint yh = y >> 128; uint xlyl = xl * yl; uint xlyh = xl * yh;
  uint xhyl = xh * yl; uint xhyh = xh * yh;

  uint ll = uint128 (xlyl);
  uint lh = (xlyl >> 128) + uint128 (xlyh) + uint128 (xhyl);
  uint hl = uint128 (xhyh) + (xlyh >> 128) + (xhyl >> 128);
  uint hh = (xhyh >> 128); l = ll + (lh << 128);
  h = (lh >> 128) + hl + (hh << 128);
}
```

和

```
function fullDiv (uint l, uint h, uint z)
public pure returns (uint r) {
  require (h < z); uint zShift = mostSignificantBit (z);
  uint shiftedZ = z;
  if (zShift <= 127) zShift = 0;
  else
  {
    zShift -= 127;
    shiftedZ = (shiftedZ - 1 >> zShift) + 1;
  } while (h > 0)
  {
    uint lShift = mostSignificantBit (h) + 1;
    uint hShift = 256 - lShift; uint e = ((h << hShift) + (l >> lShift)) / shiftedZ;
    if (lShift > zShift) e <<= (lShift - zShift);
    else e >>= (zShift - lShift); r += e; (uint tl, uint th) = fullMul (e, z);
    h -= th;
    if (tl > l) h -= 1;
    l -= tl;
  } r += l / z;
}
```

这里的`mostSignificantBit`是一个函数，它返回自变量最高有效位的从零开始的索引。该功能可以如下实现:

```
function mostSignificantBit (uint x) public pure returns (uint r) {
  require (x > 0); if (x >= 2**128) { x >>= 128; r += 128; }
  if (x >= 2**64) { x >>= 64; r += 64; }
  if (x >= 2**32) { x >>= 32; r += 32; }
  if (x >= 2**16) { x >>= 16; r += 16; }
  if (x >= 2**8) { x >>= 8; r += 8; }
  if (x >= 2**4) { x >>= 4; r += 4; }
  if (x >= 2**2) { x >>= 2; r += 2; }
  if (x >= 2**1) { x >>= 1; r += 1; }
}
```

上面的代码非常复杂，可能应该解释一下，但是我们现在将跳过解释，专注于不同的问题。这段代码的问题是每次调用`mulDiv`函数大约消耗 2.5K 的 gas，相当多。所以，

## 我们能便宜点吗？

> 剧透:mathemagic！

下面的代码是基于 Remco Bloemen 描述的令人兴奋的数学发现。如果你喜欢这段代码，请点击他的“数学魔术”文章。

首先，我们重写`fullMul`函数:

```
function fullMul (uint x, uint y)
public pure returns (uint l, uint h)
{
  uint mm = mulmod (x, y, uint (-1));
  l = x * y;
  h = mm - l;
  if (mm < l) h -= 1;
}
```

每次调用`fullMul`可以节省大约 250 点汽油。

然后我们重写`mulDiv`函数:

```
function mulDiv (uint x, uint y, uint z)
public pure returns (uint) {
  (uint l, uint h) = fullMul (x, y);
  require (h < z); uint mm = mulmod (x, y, z);
  if (mm > l) h -= 1;
  l -= mm; uint pow2 = z & -z;
  z /= pow2;
  l /= pow2;
  l += h * ((-pow2) / pow2 + 1); uint r = 1;
  r *= 2 - z * r;
  r *= 2 - z * r;
  r *= 2 - z * r;
  r *= 2 - z * r;
  r *= 2 - z * r;
  r *= 2 - z * r;
  r *= 2 - z * r;
  r *= 2 - z * r; return l * r;
}
```

这个实现每次`mulDiv`调用只消耗大约 550 gas，还可以进一步优化。比学校数学方法好 5 倍。非常好！但是一个人真的必须获得数学博士学位才能写出这样的代码，而且不是每个问题都有这样的数学魔法解决方案。如果可以，事情会简单得多

# 在可靠性中使用浮点数

正如我们在本文开头已经说过的，在 JavaScript 中，人们只需简单地写下`a * b / c`，语言会处理剩下的事情。如果我们能在可靠性方面做同样的事情会怎么样？

事实上我们可以。虽然核心语言不支持浮点，但有一些库支持浮点。例如，使用 [ABDKMathQuad](https://github.com/abdk-consulting/abdk-libraries-solidity/blob/master/ABDKMathQuad.md) 库，可以编写:

```
function mulDiv (uint x, uint y, uint z)
public pure returns (uint) {
  return
    ABDKMathQuad.toUInt (
      ABDKMathQuad.div (
        ABDKMathQuad.mul (
          ABDKMathQuad.fromUInt (x),
          ABDKMathQuad.fromUInt (y)
        ),
        ABDKMathQuad.fromUInt (z)
      )
    );
}
```

不像 JavaScript 那样优雅，不像 mathemagic solution 那样便宜(甚至比学校的数学方法更具扩展性)，但是简单明了而且相当精确，因为这里使用的四倍精度浮点数有大约 33 位有效小数。

该实现一半以上的气体消耗用于将`uint`值转换为浮点值并返回，比例计算本身仅消耗约 1.4K 气体。因此，在所有智能合约中使用浮点数比混合使用整数和浮点数要便宜得多。

# 结论

由于溢出和缺乏分数支持，百分比和比例在可靠性方面具有挑战性。然而，各种数学技巧允许正确和有效地解决比例。

库支持的浮点数可能会让生活变得更美好，但代价是气体和精度。

在我们的下一篇文章中，我们将深入金融数学，因为下一个主题将是: [**复利**](/coinmonks/math-in-solidity-part-4-compound-interest-512d9e13041b) 。

本系列的其他文章:

*   [第 1 部分:数字](/coinmonks/math-in-solidity-part-1-numbers-384c8377f26d)
*   [第二部分:溢出](/coinmonks/math-in-solidity-part-2-overflow-3cd7283714b4)
*   [第四部分:复利](/coinmonks/math-in-solidity-part-4-compound-interest-512d9e13041b)
*   [第五部分:指数和对数](/coinmonks/math-in-solidity-part-5-exponent-and-logarithm-9aef8515136e)

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) |加密交易机器人
*   [杠杆代币](/coinmonks/leveraged-token-3f5257808b22)终极指南