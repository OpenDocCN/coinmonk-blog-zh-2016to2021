# 推荐系统的现状

> 原文：<https://medium.com/coinmonks/the-current-state-of-recommender-systems-7153e12c5c0b?source=collection_archive---------5----------------------->

## 或者，为什么每次我还是会被提示去看那部糟糕的亚当·桑德勒电影？

![](img/404a9c338116996fe060d839f21d0267.png)

*Photo by Charles Deluvio on Unsplash*

## 序幕

说实话——AI 不仅仅是一个东西。AI 是巨大的。我们不仅在谈论最近媒体报道的数量，还在谈论它改变我们所知世界的潜力。专家们大多不再争论“如果”，而是“何时”。

随着关于大数据驱动的神经网络的突破的消息开始在媒体上更加普遍地传播，它引发了一种兴趣，这使我陷入了兔子洞。从一些轻松的阅读开始，我前往 Udacity 纳米学位的[深度学习](https://eu.udacity.com/course/deep-learning-nanodegree--nd101)和[人工智能](https://eu.udacity.com/course/ai-artificial-intelligence-nanodegree--nd898)，寻求更好地理解这个话题。

一年半后，新闻故事似乎仍在快速产生，关于该主题的科学论文每周都有数十篇发表。

最近在伦敦举行的 [Infiniteconf 2018](https://skillsmatter.com/conferences/9780-infiniteconf-2018-the-conference-on-big-data-and-ai) 期间，推荐系统在几乎任何涵盖大数据或人工智能的演讲中都获得了专门的演讲和荣誉点头。

当然，下面的一些可能是由我个人的过滤泡沫精心策划的，但它肯定是值得了解正在发生的事情。

## 什么是推荐系统？

让我们看看从爆炸式增长的数据量和可用计算能力中获得巨大好处的众多事物之一。推荐者或推荐系统可能和交易本身一样古老。它的要点是，基于你过去喜欢或享受或购买的东西，某人或某事暗示了你可能也会感兴趣的类似事物。

> 我看你喜欢穿羊毛绒乐福鞋，拥有整只羊怎么样？当然是有价格的。
> ——可能是公元前 10000 年美索不达米亚的某个人

不确定在网飞看什么？谷歌搜索近年来世界部分地区是如何变成政治混乱的？需要另一个韦伯大师触摸烧烤炉，类似于你上周在亚马逊上买的那个？当你打错“鸭子”的时候你的手机会提醒你吗？你猜对了，推荐系统拯救了你。

由于世界范围内不知情的顾客太多，现在应该很清楚他们在做什么。但是它们是如何运作的呢？

## 简史

![](img/11c01a61e06903e4d81151fe1c27abcc.png)

Photo by [Tim Mossholder](https://unsplash.com/photos/ZHfFvW2u93U?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/vendor?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

大多数早期的系统都是由人工(猜测)得出的。有点像:“先生，你在等着补鞋的时候要不要来一把钥匙？”。自从网上购物出现以来，这种方式已经被自动化程度稍高的系统所取代。某些类别的产品被捆绑在一起，或者通常是组合购买，以标记购买典型组合的一部分的客户。想喝点啤酒配点心吗？我当然知道！

改进主要是由数据工程师或公司其他人做出的，他们想出了新的类别或配对来尝试。

最近，任何有钱的人都通过收集大量用户创建的反馈和数据，如星级评定，将其输入神经网络并等待消化，从而将他们的系统提升到一个新的水平。结果是，一台更好、更便宜、可扩展的销售机器，为个人买家量身定制。

## 当前状态

有许多途径可以得到类似的结果，但这里有一个简短的描述，作为这些系统的一个例子，网飞算法是如何[当前](http://www.wired.co.uk/article/how-do-netflixs-algorithms-work-machine-learning-helps-to-predict-what-viewers-will-like) [运行](https://www.radiotimes.com/news/2017-09-08/netflix-reccomendation-how-does-it-work/)的。

这一切都始于大量的数据。

一方面，这些数据来自用户对他们观看的某些电影或节目的明确评级或喜欢程度。它也可能来自隐性来源，比如在短时间内狂看一整部电视剧。人们说他们在看的东西(纪录片、政治剧……)往往与他们实际消费的东西不符([爱情岛](https://www.imdb.com/title/tt4770018/)，……)。

另一方面，网飞也在剖析他们提供给用户的内容。这可以从简单地标记电影的类型，到对他们的视频材料的场景、人物或风格进行更加细致入微的分析。关键是，要弄清楚哪种类型的用户被哪种类型的内容所吸引，原因是什么。

![](img/27c7fa2101232ded9b376175f9db9a8b.png)

Photo by [John Carlisle](https://unsplash.com/photos/l090uFWoPaI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/neural-network?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

这就是机器学习发挥作用的地方。神经网络的任务是给数百个不同的输入变量分配权重，并决定哪些因素在决定任何给定用户接下来可能对哪个材料感兴趣方面起着最大的作用。如果有足够的输入和计算资源，他们在这方面做得很好。

虽然这个系统仍然有它的缺陷，但是大约 80%的网飞用户现在正在使用该公司的推荐来发现下一个要狂欢的东西。鉴于这项服务拥有超过 1 亿的独立用户，这是一个巨大的数字。

如果你想从各种实现中找到一些不同例子的更深入的解释，看看 Alex Smola 的这个有趣的演讲。

## 在不久的将来

最先进的机器学习正在快速发展。来自学术界和工业界的巨额投资正在创造有前途的新系统和想法。

最近成为头条新闻的一种方法叫做[强化学习](https://en.wikipedia.org/wiki/Reinforcement_learning)。简单来说，这相当于人工智能中的放任式养育。不是告诉你的算法所有的参数看起来是什么样子，它们是如何相关的，并给出每个输入的期望结果的例子，而是给软件一个目标和对输入数据的访问。其余的应该自己解决，通常是通过大量的试验和错误。

来自 DataTonic 的 Oliver Gindele 在 Infiniteconf 上就这个话题做了一次非常有趣的演讲。他还认为，在不久的将来，基础模型将变得越来越复杂，越来越不死板。他们可以根据需要从不同的移动部件中提取数据，以提高推荐的准确性。

## 收场白

推荐系统有时会被发现，有时会惨败。(深度)神经网络做得很好，正在推动当前的改进，但仍有空间建立更精确的系统。请放心，大量的预算正流入研究领域，让我们在享受购物乐趣的同时花更多的钱。

如果你想了解更多关于推荐系统或人工智能的知识，我可以强烈推荐 Udacity 的课程和纳米学位！他们还引入了一个[数据科学家纳米学位](https://eu.udacity.com/course/data-scientist-nanodegree--nd025)，现在它涵盖了推荐系统，作为他们让学生构建的项目之一。