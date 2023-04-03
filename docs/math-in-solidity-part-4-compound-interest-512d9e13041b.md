# 坚实的数学(第四部分:复利)

> 原文：<https://medium.com/coinmonks/math-in-solidity-part-4-compound-interest-512d9e13041b?source=collection_archive---------1----------------------->

## 本文是关于扎实做数学系列文章的第四篇。这次的题目是:**复利**。

![](img/0de65433c959848b64ed9b1a5f5a73b1.png)

# 介绍

在我们的[上一篇文章](/coinmonks/math-in-solidity-part-3-percents-and-proportions-4db014e080b1)中，我们讨论了百分比以及它们是如何在可靠性中计算的。在金融数学中，百分比通常与贷款和存款的利息有关。在每个时期结束时，比如一个月或一年，一定百分比的本金支付给贷方或存款人。这种模式称为单利，每期支付的百分比称为定期利率。

在计算机程序中，用利率代替利率是很常见的。例如，对于 3%的利率，该比率为 0.03。因此，一段时期的利息支付额可以计算为利息率乘以本金金额，从上一篇文章中我们已经知道如何有效而精确地完成这一操作。

简单的利息模式很简单，但是如果利息不是立即支付给贷方或存款人，而是添加到本金中，事情就会变得复杂。在这种情况下，过去期间累积的利息会影响将来收取的利息金额。

在本文中，我们将讨论如何在 Solidity 中实现这种模式，这种模式的名称是:**复利**。

# 周期性复利

我们已经知道如何计算单利。计算复利的直接方法是在每个时间段结束时计算单利，然后将计算出的利息与本金相加。在高级语言中，比如 JavaScript，它看起来像这样:

```
principal += ratio * principal; // Do after each time period
```

这里`ratio`几乎肯定是分数，但是 Solidity 不支持分数，所以在 Solidity 中我们应该这样写:

```
principal += mulDiv (ratio, principal, 10^18);
```

我们使用上一篇文章中的`mulDiv`函数，并假设`ratio`是一个小数点后有 18 位小数的定点数。

上面的代码在大多数情况下都能工作，但是它的`+=`操作可能会溢出，所以为了保证代码的安全，我们需要像这样修改它:

```
principal = add (principal, mulDiv (ratio, principal, 10^18));
```

这种变体对于生产来说可能是好的，但是难以阅读。在本文中，为了简单起见，我们将使用简单的算术运算，就好像坚固性将支持分数，并且算术运算不会溢出。在实际代码中，这些操作应该用适当的函数来代替。

一旦我们知道如何计算单个周期的复利，问题就是:

## 我们如何在每个时间段结束时触发复利？

> 剧透:我们不应该

与传统应用程序不同，智能合约不能有任何后台活动。契约的字节码仅在事务调用契约时执行，无论是直接调用还是通过另一个智能契约调用。人们可以依靠第三方服务，如 [Provable](https://provable.xyz/) (以前称为 Oraclize)，定期调用特定的智能合同，或者可以经济地激励普通人这样做。

这种方法可行，但有许多缺点。首先，有人要为汽油付费，所以汽油不是免费的。第二，即使没有人会在接下来的时间内获得更新的本金，利息也必须在每个周期结束时复利。第三，时间周期越短，必须执行的复合越频繁，因此消耗的气体越多。第四，对于短时间周期，这种方法是不准确的，因为事务挖掘时间是不可预测的，并且在高网络负载时可能相当大。

所以，如果在每个期末复利不是一个稳健的好主意，那么

## 什么时候应该复利？

> 剧透:“懒”复利

更好的方法不是在每个时间段结束时复利，而是仅在有人需要存取本金金额或债务或存款时复利，并且在此时对自上次复利以来结束的所有时间段执行复利:

```
uint currentPeriod = block.timestamp / periodLength;
for (uint period = lastPeriod; period < currentPeriod; period++)
  principal += ratio * principal;
lastPeriod = currentPeriod;
```

这段代码将所有尚未复利的利息加到本金中，每次有人想要访问`principal`时都必须执行这段代码。这种方法被称为“懒惰”复合，实际计算被推迟，直到有人真正需要他们的结果。

然而，上面显示的“惰性”复合的实现有一个重要的问题。实际气体消耗线性地取决于自上次执行利息复利以来经过了多少时间间隔。如果时间周期很短，或者最后一次复利是在很久以前，那么在所有过去的时间周期内复利所需的天然气量可能超过区块天然气限制，实际上使进一步复利成为不可能。所以问题是:

## 如何更高效地做“懒”复利？

> 剧透:间隔加倍

首先，我们注意到，单个期限的复利可以写成这样:

```
principal *= 1 + ratio;
```

对于两个时间间隔，这将是:

```
principal *= (1 + ratio) * (1 + ratio);
```

那么我们注意到，(1+*r*)= 1+(2*r*+*r*)，那么双时间区间的有效利率为 2 *r* + *r* ，其中 *r* 为单时间区间的利率。如果我们想要复利的时间间隔的数量是偶数，我们可以通过加倍时间间隔持续时间来使时间间隔的数量减半。当时间间隔的数量为奇数时，我们可以只执行一次复合，从而使剩余的时间间隔数量为偶数。代码如下:

```
function compound (uint principal, uint ratio, uint n)
public pure returns (uint) {
  while (n > 0) {
    if (n % 2 == 1) {
      principal += principal * ratio;
      n -= 1;
    } else {
      ratio = 2 * ratio + ratio * ratio;
      n /= 2;
    }
  } return principal;
}
```

上面的代码具有对数复杂度，并且当`principal`和`ratio`很大时工作良好，因此`principal * ratio`产品具有足够的有效小数以达到体面的精度。但是，如果`principal`和`ratio`很小，上面的代码可能会产生不准确的结果。现在的问题是:

## 如何提高懒复合的精度？

> 剧透:平方取幂

在上面显示的代码中，精度在以下代码中丢失:

```
principal += principal * ratio;
```

这是因为我们假设`principal`是整数，所以赋值必须舍入计算值。舍入可能被执行多次，并且舍入误差增加。

为了解决这个问题，我们可能会注意到，对于 n 个时间间隔，利息可能会像这样复合:

```
principal *= (1 + ratio) ** n;
```

这段代码在 Solidity 支持分数的情况下是可行的，但是只要它不支持分数，我们就需要自己实现指数运算。我们使用与上一节相同的对数复杂度方法，因此代码非常相似:

```
function pow (uint x, uint n)
public pure returns (uint r) {
  r = 1.0; while (n > 0) {
    if (n % 2 == 1) {
      r *= x;
      n -= 1;
    } else {
      x *= x;
      n /= 2;
    }
  }
}function compound (uint principal, uint ratio, uint n)
public pure returns (uint) {
  return principal * pow (1 + ratio, n);
}
```

注意表情:`r = 1.0`。这里要记住的是，我们在这里处理分数，就好像坚实度确实支持它们，而实际上并不支持。人们将不得不用实现分数数学函数来代替所有的算术运算。例如，下面是使用 [ABDK Math 64.64](https://github.com/abdk-consulting/abdk-libraries-solidity/blob/master/ABDKMath64x64.md) 库实现 64.64 位定点数算术运算的真实代码:

```
function pow (int128 x, uint n)
public pure returns (int128 r) {
  r = ABDKMath64x64.fromUInt (1); while (n > 0) {
    if (n % 2 == 1) {
      r = ABDKMath64x64.mul (r, x);
      n -= 1;
    } else {
      x = ABDKMath64x64.mul (x, x);
      n /= 2;
    }
  }
}function compound (uint principal, uint ratio, uint n)
public pure returns (uint) {
  return ABDKMath64x64.mulu (
    pow (
      ABDKMath64x64.add (
        ABDKMath64x64.fromUInt (1), 
        ABDKMath64x64.divu (
          ratio,
          10**18)),
      n),
    principal);
}
```

实际上，这个库已经有了`pow`函数，可以用来代替我们的实现。

上面的代码非常精确和简单，但是它只适用于离散的时间间隔。如果我们需要计算任意时间间隔的复利呢？这种模式被称为

# 连续复合

连续复利的思想是计算任意时期的利息，而不是固定时期的利息。实现这一点的一种方法是使用分数周期数。我们已经知道如何计算 n 个周期的复利:

```
principal *= (1 + ratio) ** n; 
```

假设时间周期为一年，我们想计算 1 个月的复利，即一年的 1/12。那么公式应该是:

```
principal *= (1 + ratio) ** (1 / 12);
```

不幸的是，上面给出的实度和`pow`函数都不支持分数指数。我们可以通过整数幂和根，或者通过固定底对数和指数来实现它们，但是

## 有没有更简单的方法来进行连续复合？

> 剧透:是:不要做

现实世界中的时间是连续的，或者至少看起来是这样。以太中的时间是离散的。它以秒为单位，用整数表示。因此，以 1 秒为周期的周期性复利与连续复利一样有效，因为没有人会在周期中间观察到本金值。

每秒复利的想法乍看起来可能很奇怪，但在[以太坊](https://blog.coincodecap.com/tag/ethereum/)上，它出奇地有效。3%的年利率实际上相当于每秒 0.000000093668115524%的利率，或用 18 位小数表示的每秒 0.00000000936681155 的利率。这里我们假设 1 年有 31556952 秒。

使用上述函数计算 1 年(31556952 个周期)的复利，该比率得出 2.9999999895%的年利率，因此几乎有 10 位有效数字的精度。对于大多数应用来说已经足够了。使用 128.128 位定点数而不是 64.64 位，甚至浮点可以实现更高的精度。

在我们的实验中，1 年的周期性每秒复利消耗了大约 90K 气体。对于大多数应用程序来说，这可能是可以承受的，但一般来说还是很高的。在我们的下一篇文章中，我们将介绍提供相同精度的更便宜的方法。

# 结论

复杂的分数计算，例如那些复合定期利率所需的分数计算，由于缺乏本地分数支持，在可靠性方面可能具有挑战性。

然而，复利仍然可以通过平方算法和模拟定点数的幂运算来有效地计算。

建议的方法足够强大，可以在 1 年(甚至更长)的时间跨度内复利每秒的利率。然而，这种方法很费油。

在我们的下一篇文章中，我们将提出更好的方法，下一个主题将是: [**指数和对数**](/coinmonks/math-in-solidity-part-5-exponent-and-logarithm-9aef8515136e) 。

本系列的其他文章:

*   [第一部分:数字](/coinmonks/math-in-solidity-part-1-numbers-384c8377f26d)
*   [第二部分:溢出](/coinmonks/math-in-solidity-part-2-overflow-3cd7283714b4)
*   [第 3 部分:百分比和比例](/coinmonks/math-in-solidity-part-3-percents-and-proportions-4db014e080b1)
*   [第五部分:指数和对数](/coinmonks/math-in-solidity-part-5-exponent-and-logarithm-9aef8515136e)

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)