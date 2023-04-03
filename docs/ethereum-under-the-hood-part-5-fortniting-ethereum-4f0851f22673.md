# 引擎盖下的以太坊——第 5 部分(以太坊入门)。

> 原文：<https://medium.com/coinmonks/ethereum-under-the-hood-part-5-fortniting-ethereum-4f0851f22673?source=collection_archive---------4----------------------->

**更新:语法修正**

本集假设你已经完成了本系列的第 1、2、3、4 部分，如果你还没有完成，请完成。如果你刚从第 4 部分回来，希望在这一集读到关于**帕特里夏·默克尔·特里**的一些概念，在后面的章节中，我们将讨论**帕特里夏·默克尔·特里**和**以太坊世界的一些最新发展。**

*免责声明:我不为 Epic Games 工作，也没有在堡垒之夜编写任何代码，我也不是一个专业的游戏玩家，热爱游戏。*

*提示:如果你还没有阅读第 1 部分到第 4 部分，我为你准备了一个甜味剂；向下滚动到* [***附录-A***](https://gist.github.com/deepakraous/d35635acc6d645494dba7203f1b525b5)**以获得一个小尺寸的摘要。**

1.  ***键和值。***
2.  *下一步是什么？:*

*在我们深入了解幕后发生的事情之前，我可以表达一些概念，我认为这些概念会对我们的研究有所帮助。这些概念中的一些可能是抽象的；我会试着用一些现实生活中的例子来解释；然后我们会把它们粘在一起做成一个甜甜的以太坊馅饼。这个想法并不是要掌握每一个主题，而是要有一点点的理解。*

## *键和值:*

*我们从**键( *k)*** *，*和**值( *v)*** *开始。*你会在以太坊的不同论文中发现这一点。你可以把它们想象成[](https://en.wikipedia.org/wiki/Tuple)****【乐】**而把它们表示为 **{k，v}** 。***

***一把**钥匙**就像一张秘密藏宝图，你需要**赢得**才能得到战利品。但是等一下你没有完整的路线，你只是有了一个**具体的**方向，你可以顺着这个方向去得到战利品，琢磨一下吧。***

***我将使用下面的 [**fortnite**](https://fortnite.gamepedia.com/Fortnite_Wiki) 地图，在我们所有的根中到达**潮湿的泥潭**我的战利品将会在那里，让我们想象你将会在你的藏宝图的起点被放下。还有一点要补充的是，在我们的**for nite universe**中我们制定了自己的规则，如下所示:***

***[宇宙:规则](https://gist.github.com/deepakraous/586adc5abf253b731b78eece84586abe)***

****注意:随着我们的进展，我将在*[*GitHub gist*](https://gist.github.com/deepakraous/586adc5abf253b731b78eece84586abe)*中添加这些 fortnite 规则。****

***![](img/6373961630286ac4a8dd366765352fd6.png)***

***Source:[http://fortnite.wikia.com/wiki/Moisty_Mire?file=Moisty_mire.png](http://fortnite.wikia.com/wiki/Moisty_Mire?file=Moisty_mire.png)***

***让我们按顺序在地图上列出所有的地方。现在，在点这些地方时，先停下来想一想。我将列出它们，如下图所示:***

***`{Junk Junction, Anarchy Acres,Haunted Hills, Pleasant Park,....}`***

***因此，我按照到达"**潮湿沼泽"**的最具挑战性的方式对它们进行了排序，现在让我们在这里创建一个新规则，并使用地图中的两个数字(k)，例如，**闹鬼的山丘**将是" **HH** "***

***`{ **{ RR,AA,HH,PP… }**, **“MOISTY MIRE”** },` 因此有了`**{Key, Value}**`***

***现在想象有很多这样的隐藏战利品，比如我称之为**“IAMINA CULT”**的那个，并且你有另一张你刚刚**获得的地图**。***

***`{ {DD,RR,FF,… }, “IAMINA CULT” }`***

***让我们玩另一个游戏，想象一下将所有这些键放在一个大桶中，我们称之为**for nite Keys**and Value(s ),放在另一个名为 FortNiteValues 的桶中，FortNiteUniverse 是一个键和值的集合，我们可以将它正式表示为**for nite universe、****for nite Keys**、**for nite treasures**:***

***有限宇宙数据结构***

## ***接下来呢？：***

***在下一集里，我们将创建大量的**规则**，并将为所有那些讨厌的 **FortNiteKeys** 和**fort nite treasures**添加一个被称为**捷径**的特定法律，我们将称之为**[**Hashing**](/@derao512/ethereum-under-the-hood-part-6-hashing-31077c7659ca)**。在那之前，继续学习。*******

*******参考文献:*******

*****[](https://ethereum.stackexchange.com/questions/40069/key-value-pair-in-account-storage-and-corresponding-modified-merkle-patricia-t) [## 帐户存储中的(键，值)对和相应的修改的 Merkle patricia 树

### 在帐户的存储内容中，这些(k，v)是什么，即键和值？合同代码可以自由使用这些…

ethereum.stackexchange.com](https://ethereum.stackexchange.com/questions/40069/key-value-pair-in-account-storage-and-corresponding-modified-merkle-patricia-t) 

# 附录- A

[来源](https://gist.github.com/deepakraous/d35635acc6d645494dba7203f1b525b5) : recap-1.md*****