# 在 R 中创建因子并重新排序因子级别

> 原文：<https://medium.com/coinmonks/creating-factors-and-re-ordering-factor-levels-in-r-8cad2edba915?source=collection_archive---------2----------------------->

![](img/4ade95c22f6f5d1314573cf0da3a19c0.png)

“Letter buttons on an old, dirty jukebox.” by [Diomari Madulara](https://unsplash.com/@diomari?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在 R 中处理数据时，您可能希望将数值转换成因子，以便于探索性的数据分析或模型构建。如果 R 选择的缺省值不令人满意，您可能还希望控制因子的顺序。在这篇博文中，我将涵盖这两种用例。

## **将数值改变为系数**

在 r 中，将数值更改为因子非常容易。通常，您可能需要更新一列或多列的值。这是这样做的:

```
> numericValues = (sample(1:5,100, replace=TRUE))
> levels(numericValues)
NULL
```

我们可以使用 levels(numericValues)来确认不存在，实际上调用返回 NULL。

现在，如果我们希望将这些值转换成因子，我们可以简单地使用 factor():

```
> numericValues <- factor(numericValues)
> levels(numericValues)
[1] “1” “2” “3” “4” “5”
```

## **改变因素的等级顺序**

现在，我们可能希望颠倒我们的因素的级别，因此 5 是最低的，1 是最高的。这可以通过将 factor()与 levels()结合使用来轻松实现:

```
> numericValues <- factor(numericValues, levels = c(5,4,3,2,1))
> levels(numericValues)
[1] “5” “4” “3” “2” “1”
```