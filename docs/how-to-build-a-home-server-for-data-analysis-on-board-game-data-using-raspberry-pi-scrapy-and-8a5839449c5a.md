# 如何使用 Raspberry Pi，Scrapy 和 Airflow 构建一个家庭服务器，用于棋盘游戏数据的数据分析

> 原文：<https://medium.com/coinmonks/how-to-build-a-home-server-for-data-analysis-on-board-game-data-using-raspberry-pi-scrapy-and-8a5839449c5a?source=collection_archive---------0----------------------->

如果你玩一些桌游，你可能知道[桌游极客](https://www.boardgamegeek.com/) (BGG)，这是一个面向桌游爱好者的网站。BGG 有一个排名系统，给你每个游戏的当前排名状态。然而，如果你想对历史数据进行数据分析，看看上周哪场比赛表现最好，这就变得棘手了，因为数据不再可用。

在本帖中，我们将探讨如何使用 **Scrapy** 从公共网站获取数据的排名页面…