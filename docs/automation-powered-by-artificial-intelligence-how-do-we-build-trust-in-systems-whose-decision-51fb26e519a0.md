# 人工智能驱动的自动化:我们如何在决策过程无法理解的系统中建立信任？

> 原文：<https://medium.com/coinmonks/automation-powered-by-artificial-intelligence-how-do-we-build-trust-in-systems-whose-decision-51fb26e519a0?source=collection_archive---------13----------------------->

![](img/c799f1fdc3b3e3a8e9f35a9b1bb688c8.png)

Photo by [Markus Spiske](https://unsplash.com/photos/Skf7HxARcoc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/blockchain?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

似乎每天都有关于人工智能(AI)完成一些壮举或解决一些新问题的新故事在媒体上发表。从无人驾驶汽车到预测患者的疾病风险，人工智能驱动的系统正越来越多地获得对决策的更多控制。

然而，这些系统的运作方式使得它们的内部运作极其不透明。虽然有些人认为理解和解释人工智能系统的决策过程对于让它们自主运行至关重要(否则如何对错误负责？还有，杀手机器人，有人知道吗？)，其他人认为这不是问题，只要他们做得足够好(这不像我们完全理解每个人是如何思考的，然而当我们登机或乘坐出租车时，我们把我们的生命交到他们手中)。

在这篇文章中，我认为，为了充分利用可以由人工智能自主执行的应用，或许应该将模型的内在复杂性作为操作环境的一个特征来接受，而不是将重点放在让监管者、人工智能专家和商业专家共同努力制定框架，明确定义自主操作所需的好处、风险和限制。

# 是什么让人工智能决策过程如此难以理解

目前在许多媒体中被称为人工智能的，以及本文中讨论的，是称为机器学习的领域的一个子集。这涉及算法，当输入训练数据时，这些算法能够学习和调整自己，以便在它们以前不知道的情况下执行任务。

由于机器学习算法能够理解和利用数据中的复杂关系，许多企业都使用机器学习算法来获得洞察力和进行预测。此外，机器学习的一个子集也是最近新闻中看到的许多突破性技术发展的原因，如特斯拉的自动驾驶仪，或谷歌的许多产品(个人助理，照片分类，翻译等)。).这些就是所谓的深度神经网络。

深度神经网络通过构建具有许多隐藏层的结构来执行其任务，这些隐藏层对输入给它们的数据中的复杂关系进行建模。这使得他们能够在各种环境中出色地完成任务。然而，这些复杂的结构通常是神秘的，因为它们不能追溯到源数据的特定属性或行为。这种解释结果的困难，再加上在许多情况下，这些算法不断得到数据，因此随着时间的推移而演变，这使得找出导致软件做出一个决定而不是另一个决定的逻辑变得极其困难，甚至是不可能的。

![](img/3facb24c4e7492b6bb6170782040250c.png)

Simple Neural Network with 8 input features (top row), two hidden layers (middle), and 2 possible outputs (bottom)

# 自动化框架基于风险和性能，而不是模型的可解释性

目前，机器学习主要用于通过提供数据价值甚至预测未来行为来帮助人类做出更好的决策。但是，即使应用程序不断增长，并越来越多地显示出它们的价值，委托这些模型进行自主决策也涉及到在每种新情况下它将如何表现的“信念的飞跃”。无论它们可以被训练得多好，总会有模型出错的情况，并且随着应用数量的增加，在某些情况下，这种错误可能会产生重大影响。

出于这个原因，这些系统中的大多数仅用于支持和促进人类决策，并且规章通常与应该有人类因素参与的理念相一致。这一点可以在新的欧洲数据保护法规(GDPR)中看到，该法规规定，公司必须披露任何完全基于算法(即自动化)做出的决策，用户可以质疑此类决策，并请求人的“第二意见”。

![](img/27f18382048e56cae4074b29515cd98b.png)

Photo by [Markus Spiske](https://unsplash.com/photos/Skf7HxARcoc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/blockchain?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

然而，这一政策严重限制了可通过人工智能开发的潜在未来应用。与其试图使人工智能决策过程合理化，并确保每个决策都得到人类的支持，不如将重点转移到建立框架上，以提供有关性能和风险的清晰画面，从而决定哪些系统可以自主决策。人工智能专家和商业专家将一起工作来定义这样一个系统的预期收益，以及通过风险管理技术来获得对风险(可能性和错误的影响/严重性)的清楚理解。这将使我们能够理解风险是否与收益相称。根据自动化的应用，监管机构可以帮助定义给定风险类别允许的容忍度。

# 结论

尽管许多人在日常生活中使用他们并不完全理解的系统，但他们相信该领域的专家对其行为有详细的了解。如果他们被告知信任一台机器，比如一辆自动驾驶汽车，知道他们的决策过程无法完全解释，他们肯定会感到一些担忧。但也许这只是需要克服的偏见，特别是如果可以证明他们在某些任务上的表现可以比单独的人类好几个数量级，并且风险以适当的方式管理。