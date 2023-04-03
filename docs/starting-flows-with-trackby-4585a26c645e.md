# 使用 trackBy 启动流

> 原文：<https://medium.com/coinmonks/starting-flows-with-trackby-4585a26c645e?source=collection_archive---------4----------------------->

![](img/9bcf55286c98e658e516e893f92e3364.png)

[New York](https://pixabay.com/en/new-york-city-brooklyn-bridge-night-336475/) by [Pixabay Free photos](https://pixabay.com/en/users/Free-Photos-242387/)

继续我关注 Corda 服务的趋势，我有更多的技巧来帮助你的 CorDapp 顺利工作。这一次，我们将把重点放在使用`trackBy`从服务内部启动流程，以及如果不小心可能出现的离散问题。

这应该是一个相对较短的帖子，因为我可以依靠我以前帖子中的工作: [Corda 服务 101](https://lankydanblog.com/2018/08/19/corda-services-101/) 和[Corda 服务的异步流调用](https://lankydanblog.com/2018/09/22/asynchronous-flow-invocations-with-corda-services/)。在[使用 Corda 服务的异步流调用](https://lankydanblog.com/2018/09/22/asynchronous-flow-invocations-with-corda-services/)中找到的内容与本文非常相关，并将包含本文中未包含的额外信息。

这篇文章适用于 Corda 开源和企业。撰写本文时的版本是开源`3.2`和企业`3.1`。

# trackBy 简介

`trackBy`允许您编写包含指定类型状态的事务完成时执行的代码。无论它们是作为输入还是输出，代码仍然会被触发。

从这里，你可以决定你想要它做什么。也许是非常简单的事情，比如记录已经收到的状态。或者，可能是一些更有趣的事情，比如启动一个新的流程。这个用例非常适合这个特性。一旦一个节点接收到一个新的状态或者消耗了一个新的状态，它们就可以启动一个新的流，该流代表工作流中的下一个逻辑步骤。

此外,`trackBy`有两个版本。一个是我一直提到的`trackBy`，可以在 CorDapp 中使用。另一个是`vaultTrackBy`，使用 RPC 从节点外部调用。

本帖中出现的问题只出现在 CorDapp 版本中，`trackBy`。因此，我们将在这篇文章的剩余部分排除`vaultTrackBy`。

# 这个离散问题是什么？

僵局。当我这样说的时候，它并不是非常离散的。但是，它发生的方式相当微妙，需要很好地理解正在发生的事情才能弄清楚。如前所述，这个问题非常类似于在[使用 Corda 服务的异步流调用](https://lankydanblog.com/2018/09/22/asynchronous-flow-invocations-with-corda-services/)中详述的问题。此外，当我在一个项目中遇到这个问题时，R3 诊断了这个问题，我相信他们会解决这个问题。在那之前，如果你遇到同样的问题，这篇文章会让你省去一些挠头的麻烦。

我将引用我在以前的帖子中写的内容，因为它的解释只缺少了关于这篇帖子的一点。

> *流工作队列负责流的执行顺序，并在添加和完成流时填充和清空。这个队列对于协调节点中流的执行至关重要。当涉及到多线程流时，这也是痛苦的来源。*

![](img/2724d76a6b1965e2777d15a75a160e7d.png)

> *我为什么要谈论这个队列？嗯，我们需要格外小心，不要让无法完成的流填满队列。*
> 
> 怎么会这样？通过启动正在执行的流中的一个流，然后等待其结束。在队列的线程池中的所有线程都遇到这种情况之前，这不会造成问题。一旦发生这种情况，队列就会陷入死锁。没有流可以完成，因为它们都依赖于许多排队的流来完成。

![](img/dd83e5eaa7f5c71738aca01dd4f9f44f.png)

这标志着我的复制意大利面的结束。不过我还是要说，真的，我建议你通读一下[使用 Corda 服务的异步流调用](https://lankydanblog.com/2018/09/22/asynchronous-flow-invocations-with-corda-services/)以获得对这个主题的全面解释。

这跟`trackBy`有什么关系？从服务中调用`trackBy`将在流工作线程上运行每个可观察到的事件。换句话说，每个事件在队列中占据一个位置。从这里开始一个流将向队列中添加另一个项目，并挂起当前线程，直到流结束。它将一直留在队列中，直到那个时候。如果你最终处于这样一种情况，队列中的所有点都被可观察到的事件占据，而不是实际的流，我有一个词送给你。僵局。这与我之前详述的情况完全相同，但起点不同。

从好的方面来看，解决方案是小菜一碟(这个说法到底是从哪里来的？).

# 问题被修复的部分

现在你知道问题是什么了。将一个“中断的”版本修改成一个避免死锁的版本只需要几行额外的代码。

让我们来看看一些代码，这些代码非常类似于导致我踏上这颗地雷的原因:

每当节点接收到新的`MessageState`时，该服务使用`trackBy`来启动新的流，由于前面提到的所有原因，该代码有可能死锁。我们不知道什么时候，或者是否会发生。但是，有可能。所以我们应该在它成为问题之前解决它。

下面的代码将做到这一点:

我添加了一些注释，以便更清楚地说明由于只添加了几行而发生的变化。

所有这些变化只是在一个新的线程上启动了这个流程。这将允许当前线程结束。记住，这很重要，因为这个线程在队列中占有一个位置。允许它结束，为接下来的事情释放一个位置。不管它是来自`trackBy`的另一个可观测的事件还是一个流。没关系。只要线程被释放，由于这个代码而发生死锁的可能性为零。

# 把你从这条线中释放出来

请花一点时间沉浸在我在这部分标题中所做的双关语的荣耀中。也许没那么好，但我还是以此为荣。

总之，在 Corda 服务中使用`trackBy`非常适合根据保存到节点的信息启动新流程。但是，当从一个`trackBy`可观测的开始新的流动时，你需要小心。这是由于可观察到持有流工作线程，因此在队列中的位置。如果吞吐量达到更高的数字，就有节点死锁的风险。您可能会遇到这样的情况:队列被等待流完成的线程阻塞，但是队列中没有实际的流。通过将流调用移动到一个独立于可观察线程的线程上。您允许队列中曾经被占用的位置被释放。现在你的`trackBy`代码不会导致死锁。

这篇文章中使用的代码可以在我的 [GitHub](https://github.com/lankydan/corda-service-trackby-flows) 上找到。

如果你觉得这篇文章很有帮助，你可以在 Twitter 上关注我，地址是 [@LankyDanDev](http://www.twitter.com/LankyDanDev) 来了解我的新文章。

我帖子中的观点和看法是我自己的，不代表埃森哲在任何问题上的观点。[查看丹·牛顿的所有帖子](https://lankydanblog.com/author/danknewton/)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)

*原载于 2018 年 10 月 5 日*[*【lankydanblog.com】*](https://lankydanblog.com/2018/10/05/starting-flows-with-trackby/)*。*