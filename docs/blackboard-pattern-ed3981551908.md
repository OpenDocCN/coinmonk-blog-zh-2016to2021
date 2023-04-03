# 黑板模式

> 原文：<https://medium.com/coinmonks/blackboard-pattern-ed3981551908?source=collection_archive---------3----------------------->

![](img/9d543be03a890774a18902b72cb16a52.png)

# 为什么使用黑板模式

所以我意识到距离我的上一篇文章已经过去了一个多星期——我并没有开始耗尽我的想法，我只是从机器人学中抽出一个星期来赶上其他事情。

今天的主题将着眼于黑板模式，以及它如何应用于机器人。粗略地说，黑板模式是一种软件设计模式，有三个组成部分:

1.  存储世界信息的共享空间(黑板)。
2.  一组算法(机器学习或其他),从世界上读取信息，并将结果发布到黑板上。
3.  一组从黑板上读取结果并相应更新机器人的模块。

例如，一个机器人可以有两个摄像头，一个指向桌子，另一个指向某个目标区域(可能是一个篮子)。两个摄像头的视觉算法可以独立运行，每个都将结果发布到黑板上，并允许中央模块在需要做出决定时简单地查询当前状态，而不必知道关于摄像头如何提取其特征的任何细节(或者事实上有摄像头)。

# 给黑板添加结构

对于我自己的机器人，我在黑板上使用我自己的旋转模式。我没有一个简单的“共享空间”来存储各种机器学习算法的结果，而是对数据的排列方式强加了一种结构。该结构本身基于映射算法的结果。在我的例子中，黑板基本上是一个无向图，每个顶点代表机器人世界中的一个物理位置。

当更新黑板时，当前运行的任何算法的结果与属于黑板上代表机器人当前位置的顶点的数据合并。这有几个优点:

1.  它为机器人提供了环境的“公交地图”视图，因此当它想要推理如何从一个地方到另一个地方，或者不同的物体在哪里时，它不需要直接使用占用地图。
2.  既然打算做服务机器人，那么它的大部分注意力都会集中在直接围绕它的区域(我的人类呢？我看到沙发了吗？等等？)，通过保持对当前顶点的引用，机器人可以快速提取其当前位置的最新模型，包括上次在该区域中提取的数据。

此外，还保留了前面提到的使用黑板的其他优点。然而，这种优势并不是免费的，因为所有关于世界的机器人信息都与世界上的不同位置联系在一起，当机器人需要不在其直接区域内的数据时，就需要在图中爬行以查询每个节点来获得其所需的数据。如果机器人在有许多节点的大环境中，这可能是低效的。

# 履行

那么我们如何在 ROS 中搭建黑板呢？我能想到的最通用的方法是使用服务。ROS 服务允许节点提交请求并获得对该请求的响应，这与通常的行为形成了对比，在通常的行为中，节点将向某个主题广播，但不保证任何内容都会对其做出响应。

这样做的缺点是，服务似乎需要预设的消息类型，这意味着您需要知道黑板使用这种模式所需的所有数据的格式。然而，有一种方法可以解决这个问题——我们可以简单地使用 json 与黑板进行通信。由于 json 可以被解析为字符串，并且可以用来表示我们可能会用到的几乎任何类型的数据，这就形成了一个通用的数据结构来表示我们服务的消息。

那么创造这样一个东西的基本步骤是什么呢？首先，我们需要在 ROS 中创建一个服务定义。我们的服务将接受字符串类型的数据作为输入，将类似字符串的数据作为输出(然后由客户端解析回 json)。我们在. srv 文件中表示如下:

```
string query
---
string response
```

接下来，您需要添加。srv 文件添加到项目中的 CMakeLists.txt 文件中。这将被添加到“添加服务文件”部分，类似于下面的示例:

```
add_service_files(   
  FILES    
  blackboardQuery.srv
)
```

除了上述内容之外，我们还需要让 chu 絮 _make 知道，我们希望生成在构建应用程序时使用该服务所需的头文件。为此，只需确保 CMakeLists.txt 文件中有一行类似如下的内容:

```
generate_messages(    
  DEPENDENCIES    
  std_msgs
)
```

现在，我们可以使用柳絮 _make 来生成下一步需要的消息和依赖项。一旦完成了这些，我们就可以构建基本的 ROS 服务了。假设您已经将服务命名为“blackboardQuery.srv ”,下面的代码应该可以形成一个很好的模板，用于在 C++中初始化服务。

```
**#include** <ros/ros.h>
**#include** <string>
**#include** <iostream>
**#include *"rover_platform/blackboardQuery.h"* #include *"Blackboard.cpp"* using namespace** std;
**using** json = nlohmann::json;

Blackboard g_blackboard;

**bool** blackboard(rover_platform::blackboardQuery::Request &req, rover_platform::blackboardQuery::Response &res) {
  cout << ***"BLACKBOARD: "*** << req.query << endl;
  res.response = g_blackboard.performQuery(req.query);
  **return true**;
}

**int** main(**int** argc, **char*** argv[]) {

  ros::init(argc, argv, ***"blackboard_server"***);
  ros::NodeHandle n;

  ros::ServiceServer service = n.advertiseService(***"blackboard"***, blackboard);
  ROS_INFO(***"BLACKBOARD READY"***);
  ros::spin();

  **return *0***;
}
```

为了便于解析 json 字符串，我们可以使用由 [nlohmann](https://github.com/nlohmann/json) 提供的有用的库，这个库为创建和解析 json 对象提供了一个优雅的接口。此外，它允许我们轻松地将创建的 json 对象转换成字符串格式。在进一步阅读之前，我强烈建议您查阅这个库的文档。

好了，我假设现在您至少已经浏览了我们将要使用的 json 库的文档。我们的下一步是决定通信格式。对于我的机器人，我决定使用如下格式:

```
**{
    *"Command"***: ***"setCurrentLabel"***,
    ***"data"***: **{
        ...**
    **}
}**;
```

这允许我将每个服务调用路由到黑板上的一个适当的子例程，并相应地处理所提供的数据。(在某些情况下，没有要处理的数据，而是请求返回数据)。下面是一些示例代码，展示了如何从提供的 json 中提取命令并调用适当的函数:

```
string Blackboard::performQuery(string query) {
  **auto** queryData = json::parse(query);

  string command = queryData[***"Command"***];

  cout << ***"Command: "*** << command << endl;

  **if** (command == ***"addVertex"***) **return this->**addVertex(query);
  ... Other Commands ...

  **return *"OK"***;

}
```

现在，如果我们想发回数据，我们需要使用前面讨论的模块创建自己的 json 对象。幸运的是，这相当直观，因为下面提供的用于创建顶点的 json 的代码片段应该演示了:

```
string Vertex::getJson() {
  json response = {
    {***"Vertex"***, {
      {***"x"***, **this->**x},
      {***"y"***, **this->**y},
      {***"index"***, **this->**index},
      {***"parent"***, **this->**parent},
      {***"label"***, **this->**label}
    }}
  };

  **return** response.dump();
}
```

如您所见，使用提供的插件创建 json 对象的符号相当直观——几乎是我们想要创建的实际 json 的镜像。

这应该为你考虑为你自己的机器人添加一个你自己的黑板服务提供了一个好的起点。如果你对我的精确实现感兴趣，可以在我的 github 页面上找到。

下一篇文章将涉及机器人本身的物理结构(最后一部分即将推出！).直到那时

分享享受！

> 注意:有些人可能会注意到，自从我的上一篇文章以来，它已经被推迟了一段时间——我从“机器人化”中休息了一周，好像是为了让我的脑袋休息一下。此外，为了使这个博客易于管理，我打算把它限制在每周一篇文章。毕竟，我是个业余机器人专家，不是作家。