# 以太坊治理如何工作

> 原文：<https://medium.com/coinmonks/how-ethereum-governance-works-71856426b63a?source=collection_archive---------3----------------------->

## 也许“作品”是一个强烈的词…

![](img/541c4ba9fe4c3cf4259c43026029f972.png)

# 参与者

*   *以太坊客户端开发者—* 任何维护以太坊客户端的人。这包括 Geth、OpenEthereum 和 Nethermind 开发人员以及 Besu 开发人员和维护这些客户端的 Ethereum Classic forks 的人员(在 ETC fork 维护人员的情况下，他们的参与有点特殊，我今天不会在这里深入讨论)。
*   *用户—* 使用以太坊进行交易的人。这包括使用 dapps 的人、发送/接收钱的人等等。
*   *用户授权—* 用户通常会将他们的决策权委托给第三方。Infura 和集中式交换是两个非常常见的代表。流行的 dapp 是另一类公共用户代理，用户可以访问他们喜欢的 dapp 开发链。当我们谈论用户做决定时，通常这是委托给其他地方，但最终还是用户的决定。

# 过程

每个客户都有自己的开发人员，他们有自己的动机、愿望等。每个产品的开发者自己决定他们想在他们的客户端中放什么，他们想什么时候发布他们的客户端，以及当一个更新发布时它会有什么特性。

所有核心开发调用对于客户端开发人员来说是完全可选的，但是在过去，客户端开发人员表达了保持相互兼容的愿望，因此他们通常会派一名代表参加调用，以尝试与其他客户端开发人员进行协调。

最终，每个客户端团队将发布他们认为合适的任何客户端更新，这些更新(理想情况下)将与其他客户端兼容，并且具有与其他客户端一致的分支块编号。如果客户端发布共享 fork 块触发器的兼容代码，那么当用户运行时，客户端将能够相互通信。

一旦客户端发布，客户端开发者和/或他们的支持者将向用户做广告/宣布，用户将选择是否更新他们的客户端，或者可能切换客户端。用户通常将此决策委托给第三方，如 Infura 或 centralized exchanges，但最终用户可以决定他们想要在哪个规则集上进行交易。在委托投票的情况下，如果他们不同意他们的代表，他们通常可以更换提供商(这对于托管代表来说有点棘手，因为托管人可能会拒绝在托管人不同意的链上给你钱)。

# 谁做决定？

这里没有单一的决策者。任何人都可以发布一个客户端，宣布一个客户端，分叉一个客户端(都是开源的)等等。用户可以选择使用他们想要的任何客户端。

每个客户端的核心开发人员都有一组他们个人关心的特定事情，他们通常会向其他客户端开发人员表达这些事情。如果所有的客户端开发人员都同意开发相同的功能，那么事情会进展顺利，他们会在发布时间表上协调一致。

如果客户端开发人员不能就发布什么特性或何时发布达成一致，那么他们中的一些人可能会开发、宣布和发布一个不同的特性集。我们在之前的 ETH/ETC 分裂中看到了这一点，其中并非所有的客户端开发人员都同意如何进行，因此我们最终得到了由不同组的人维护的两组不同的客户端。

# 分歧是如何处理的？

1.  没有一个核心开发人员认为这是一个好主意，他们都决定不将它集成到他们维护的客户端中。
2.  一些核心开发人员认为这是一个好主意，但不愿意放弃，而其他人认为这是一个坏主意，并愿意放弃。
3.  一些核心开发人员认为这是一个好主意，愿意分叉，而其他人认为这是不好的，不愿意分叉。
4.  一些核心开发人员认为这是一个好主意，并愿意分叉，其他人认为这是不好的，并愿意分叉。
5.  所有核心开发者都认为这是一个好主意。

*   在 1 和 5 的情况下，事情进行得非常顺利。
*   在第 2 和第 3 种情况下，通常会进行讨论，试图通过了解人们意见分歧的根本原因来达成共识，并在可能的情况下试图找到解决这些问题的可行方案，但如果没有达成“大致共识”，则信念更强的一方可能会胜出。
*   在 4 的情况下，我们有一个类似 ETH/ETC 的链分裂。

# 用户如何参与进来？

作为以太坊的用户，你可以*选择*是否运行给定的客户端，也就是说如果你认为改变不好，你可以选择干脆不升级。如果你认为最好的话，我们也欢迎并鼓励你尝试说服其他人(在适当的场合)和你一起经营一个不同的客户。

如果你有技术头脑，你也可以通过向核心开发者提出新的技术想法来参与(例如，通过 [https://ethresear.ch](https://ethresear.ch) )。您还可以通过加入现有团队、维护自己的分支或者从头构建新的客户端来参与客户端开发。

# 开发者如何决定写什么代码？

每个以太坊客户端开发者都有自己的动机，有些是众所周知的，有些是私人的。其中一些动机是财务性的(例如，他们得到了其他人的赞助，他们有自己的动机)，而其中一些动机是利他性的(例如，他们希望建立一个更加抵制审查的未来)。

然而，由于上面提到的“用户决定运行什么”特性，核心开发人员通常构建他们认为用户现在和将来想要运行的软件。大多数以太坊客户端开发人员对编写没人用的软件不感兴趣，所以最终他们通常以他们认为用户想要的，或者对用户有益的东西为导向。