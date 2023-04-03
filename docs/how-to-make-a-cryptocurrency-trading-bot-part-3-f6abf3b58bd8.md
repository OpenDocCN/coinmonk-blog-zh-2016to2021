# 如何制作加密货币交易机器人——第三部分

> 原文：<https://medium.com/coinmonks/how-to-make-a-cryptocurrency-trading-bot-part-3-f6abf3b58bd8?source=collection_archive---------1----------------------->

## 初始 Alertatron 设置和 BitMex API 集成

快速警告:

*   不是交易建议。
*   其他一些人知道建造机器人/松树等。比我好。
*   我个人与 TradingView、Alertatron、exchanges 或任何提到的服务没有任何关系。
*   我在这里展示的大多数产品我已经用了好几年了。
*   您可以通过在帖子中注册推荐链接来帮助这个项目。

> *在* [*我自己的加密机器人讨论*](https://t.me/mocbchat) 中询问关于机器人的一切

我们正在做的是:

[*零件零*](/@strategst/crypto-signals-were-a-scam-6cc240f55b40) *，我为什么要这样做*

[*第一部分*](/coinmonks/how-to-make-a-cryptocurrency-trading-bot-f94172e0c7bd) *，如何在 TradingView 上用开源策略生成信号。*

[第二部分](/coinmonks/how-to-make-a-cryptocurrency-trading-bot-7409696b617e)，BitMex 的风险管理和杠杆设置。

> 第一部分:交换执行是 bot 的第二个关键部分。执行可以让你的策略像回溯测试一样盈利，也可以让你的策略亏损。这是关于佣金、滑点、限价/市价单和许多其他微小但非常重要的事情。这是第二个教程的主题。

## **按照第 1 部分和第 2 部分的步骤，现在我们有:**

a.盈利战略

b.回溯测试结果

c.来自此策略的警报

d.风险管理系统

## 我们现在需要做的就是设置自动交易，因为我们需要所有这些自动运行。

有很多自动化服务，有其积极的一面，也有消极的一面，但是我们需要选择一个。我为我们的机器人选择了 Alertatron 服务，因为:

1.我个人用了六个多月，非常喜欢它。

2.支持电报频道非常有用。开发者本人几乎 24/7 都在那里。

3.Alertatron 是同一开发者的开源项目 [InstaBot Trader](https://github.com/instabot42/instabot-trader) 的商业版，所以你可以从 GitHub 下载该项目，并使用本教程中的命令在你自己的服务器上免费运行。

## **对于自动化执行，我们需要绑定四个东西。**

trading view—Alertatron—Exchange—Telegram。

它将如何工作？

> 当您的 TradingView 脚本想要进行交易时，TradingView 会发送一个包含特定于 Alertatron 的代码的买入或卖出警报。Alertatron 读取代码，用交易所特有的 API 语言与交易所对话，执行你的订单，设置止损，并获利。当执行时，交换告诉 Alertatron 一切都已完成，Alertatron 在电报中发送您的交换余额状态(它也有松弛和不协调集成)。这一切都在几秒钟内完成。

## 我们需要设置什么？

1.  交换:
    a .你的发送和关闭订单的 API 键。
    2。从交易视图
    a .设置所有提醒
    b .在提醒
    中粘贴你的“Webhook”链接 c .在“消息”窗口中粘贴特定于提醒的代码
2.  Alertatron:
    a .添加交换 API 密钥
    b .在收件箱中设置交易集成
    c .建立新组并添加电报集成
    d .设置 Alertatron 电报机器人
3.  电报:
    abc。几下点击。

## 从 Bitmex 获取您的 API

在 Bitmex 上注册(我的推荐链接[给你 10%的费用折扣)](https://www.bitmex.com/register/Wb1gxa)

![](img/8f9071d82b4dccc3c38013f26c651f12.png)

前往帐户偏好设置

![](img/a5653634ec79220a94944a93395e7894.png)

转到“API 密钥”

![](img/19262a641c9db639823833f691507f2d.png)

在 API 密钥中，根据需要命名您的密钥。将订单类型更改为“订单”，这将使您能够使用这些键进行交易。出于安全考虑，不要选择“撤销”，按下“创建 API 密钥”按钮会看到一个黄色窗口。

“请写下密钥”窗口只会出现一次，这意味着当你关闭这个标签，它会消失。API 密钥与登录和密码具有相同的功能，请将它们保存在安全的地方。

复制您的 ID 和密码以备后用。您可以关闭 BitMex，因为在本教程中我们不会再回到那里。

![](img/1a4c7f45c9a03276b88fd25f4ccd87b6.png)

## 设置您的闹钟启动

进入[警报器注册页面](https://alertatron.com/register)，创建账户。为了让我们的机器人在没有我们的私有服务器/VPS 的情况下工作，完全使用 Alertatron 云，我们需要购买 15 美元起的订阅，但好消息是，这个启动计划对我们来说是可以的。

点击右上角的用户名，进入付费页面。

![](img/307ca175dcb1b941f677c870ac03d2ac.png)

选择“狼”计划，自动交易，看你计划运行你的 bot 多少个月(默认为 3 个月)，给 Alertatron 的开发者送点钱！

![](img/71343b93466c4ab455a2425937efdcfb.png)

完成后，您会看到警报配额从 20 个增加到 40 个或更多。

现在是时候从交换中添加您的 API 密钥了。进行自动交易—配置 API 密钥。

![](img/648cfde08d0382058246fd7d668682d5.png)

向下滚动到这里，选择 BitMex，然后写下“MyBot”名称和您的密钥。我们使用真钱 BitMex 帐户，所以我们不检查“使用 Testnet”不倒翁。

![](img/cfb95c6b9c4d739703baa2c9c739a03b.png)

好，现在让我们做一些更复杂的事情，为我们的 TradingView 策略建立一个新的组。将不同的策略警报分发给不同的警报组是一种很好的做法。接下来我会告诉你怎么做。

转到主页—提醒— +添加新组

![](img/1368006dba6e1252da54b61f20a4c46d.png)

将其命名为 Test，然后输入关键字“MYBoT”或“MyBot ”,因为 Alertatron 不区分大小写。选取“提醒必须包含至少一个单词…”并创建新群组。

![](img/90bb274295ffb72ad3134e0a44fc8854.png)

我们现在在新的组页面上，所有从 TradingView 警报消息中被“MyBot”过滤的传入消息都将到达该页面。

![](img/f48f013612cf3912a0f8cad26483c237.png)

现在我们需要设置将这些消息转发到实际的自动交易引擎。按“加号”按钮，向下滚动到“交易”区。单击添加和配置。

![](img/5b9dd9c2115b3441a2f8b3bfa3b78672.png)

阅读条款和条件，考虑所有积极和消极因素。

![](img/97051bd631383d6eaa78bf4f7f4aa50e.png)

这是交易窗口，我们完成了。

![](img/cd0d367aa7ff8814f9c95b5d6fed4ff4.png)

## Alertatron 有一个奇妙的免费电报集成

既然我们在这里，让我们建立它。转到“测试”组页面，再次单击“加号”按钮。找到电报集成，点击“添加电报”按钮。

![](img/4c6fa94c2e3c82db5010a445c976de90.png)

在这里你可以阅读电报的步骤。

![](img/484ed49334cdb54105dddb77c34272b5.png)

您可以选择三种方式来获得电报通知:直接聊天，与机器人建立一个群组，或者将机器人添加为您频道的附加管理员。所有的设置步骤都是相似的，所以让我们简单地聊一聊。

打开你的 Telegram app，在 Telegram 中找到@alertatron_bot，按“开始”。

![](img/11ed5e29b0df221fdf9ad1d2c8a0c476.png)

Alertatron 用一条欢迎信息回答你，现在写/注册和你在 Alertatron 站点上看到的代码。

![](img/277ad18ab733919d99727f4d937849b4.png)

全部在 Telegram 中完成，现在返回到集成页面并向下滚动到底部。切换“删除内容…”并保存电报设置。

![](img/a09fc4fd76b7de23c5109389414dee5b.png)

让我们用一些消息来测试我们的电报集成。进入 Main — Alerts — Saved & Manual Alerts，编写包含“MyBot”关键字的示例消息，并在**四个**破折号下方添加文本(为了简洁起见，我们希望将代码隐藏在电报消息中)。

例如:

*MyBot 关键字
— —
这个不会显示*

切换“立即发送此警报！”然后发送出去。

![](img/57ee8c4996b4c71703064fc3013780b2.png)

返回到你的电报应用程序，看到我们有一条来自 Alertatron bot 的消息，内容是短语“MyBot keyword”。“这将不会显示”是我们想要隐藏的。

![](img/f2c7436f0263b5e870a55c264650f5c8.png)

在我们用 Alertatron 制作的这一部分中，最后一件事是获得到 TradingView 的连接链接。转到右上角，选择“传入提醒”。

![](img/d1c6f56ce5d3b75298d7eba8a8d1a70f.png)

复制这个红色的长链接，保存在一个安全的地方。它是 Alertatron 的链接密钥，允许其他服务通过它与 Alertatron 通信并执行交易。

![](img/ca905a2dd0c3eeed6a9f51e07dcd01e9.png)

太好了！在下一章中，我们将编写 Aletratron 友好的消息，并将完成 TradingView 集成。这将是开始自动交易的最后一步！

> [*在您的收件箱中直接获得最佳软件交易*](https://coincodecap.com/?utm_source=coinmonks)

[![](img/c83856be4afb4991ded5f986eda443b9.png)](https://coincodecap.com/?utm_source=coinmonks)