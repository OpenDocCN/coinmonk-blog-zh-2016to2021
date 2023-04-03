# 线性回归中的最小二乘误差到底是什么？

> 原文：<https://medium.com/coinmonks/what-actually-is-least-square-error-in-linear-regression-6e637a8a1f22?source=collection_archive---------4----------------------->

## TL；DR:通过最大化对数似然来最小化误差

![](img/0951cf33d25eb997190a129d67cdcc42.png)

image [source](https://www.inspirationde.com/image/14908/)

最小平方误差在线性回归中用作成本函数。但是，为什么要选择平方误差，而不是绝对误差，或者其他选择呢？有一个简单的证明可以表明，最小平方误差是一个合理和自然的选择。

假设目标变量和输入如下相关:

![](img/cc0bfb2a5047d99460f63619b3bdcbdd.png)

我们希望通过最大化对数似然来最小化误差。可能性函数是:

![](img/39500f53a78367776e51fbfa57f43d3a.png)

最小化对数似然函数

![](img/89bc1e15bbfe816e646d0f44538212ba.png)

，**，也称为最小平方函数**，注意，在这种情况下，σ是不相关的。

> 注意，最小二乘法对应于最大似然估计。因此，人们可以证明最小二乘法是正确的，其自然假设是ϵn(μ，σ)

![](img/56d3703e11289cc42a33c6228c7a2206.png)

image [source](https://www.foozine.com/36-fonds-decran-absolument-sublimes-et-ideals-pour-vos-smartphones-18655)

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)