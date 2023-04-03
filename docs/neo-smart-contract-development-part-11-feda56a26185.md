# Neo 智能合同开发-第 11 部分

> 原文：<https://medium.com/coinmonks/neo-smart-contract-development-part-11-feda56a26185?source=collection_archive---------9----------------------->

## C#快速循环编辑-编译-调试。NEO 智能合同

**署名—** 这个系列是[迈克尔·赫尔曼(多伦多)](https://github.com/mwherman2000)从他的 [Git 资源库](https://github.com/mwherman2000/neo-dotnetquickstart)中转载的作品。所有的掌声和赞扬都归于他。

# 目的

本练习的目的是演示如何使用 Visual Studio 快速编辑、编译和调试 C#。新智能合同。

# 目标、非目标和假设

*   尽可能缩短编辑-编译-调试周期的时间。

**假设**

您的 NEO 开发环境是按照前面的活动设置的。本活动假设您有一个符合快速入门指南的环境。

*   具体而言，这项活动基于以下假设:
*   已安装 Visual Studio 2017 社区版
*   已安装 NeoContractPlugin Visual Studio 扩展
*   新编译器(neon.exe)(调试器版本)安装在此处:

```
C:\repos\neo-debugger-tools\NEO-Compiler\bin\Debug
```

*   新调试器安装在以下位置:

```
C:\repos\neo-debugger-tools\NEO-Debugger\bin\Debug
```

*   新编译器(neon.exe)(普通版)是您搜索`PATH`环境变量的一部分

# 原则

*   提供可靠的文档:及时、准确、直观和完整
*   尽可能节省一个人的时间
*   尽可能使用开源软件

# 司机

*   NEO 中的需求。NET 开发人员社区拥有简明易懂的文档，使人们能够在尽可能短的时间内快速开发 NEO 智能合同

# C#快速循环编辑-编译-调试。NEO 智能合约:视频教程

这是为有经验的人准备的教程。NET/C#/Visual Studio 开发人员以及该平台的新手。12 点 28 分

# C#快速循环编辑-编译-调试。NEO 智能合约:逐任务教程

# 创建和调试传统的 Windows 控制台应用程序

1.  打开 Visual Studio。在 Visual Studio 中选择`File`->-`New`->-`Project...`。

![](img/a05b1b663eea7a0b765518f9597dff18.png)

2.选择`Visual C#`->T5。输入项目名称(如建议使用`NPC.yourname.QuickCycle.Client`)。从解决方案名称中删除`.Client`。点击`OK`创建解决方案和初始 Windows 控制台应用程序项目。

![](img/b89f9f7994f29bf31b7d18a7dbc2a2b4.png)

**注意**:将来，这个“客户端”Windows 控制台应用程序项目将成为调用您将在“契约”智能契约项目中创建的智能契约的离线客户端/服务器端应用程序的占位符。

3.将以下语句添加到`Main`方法中:

```
Console.WriteLine("Hello world!");
Console.ReadLine();
```

单击`Start`编译并调试您的控制台应用程序。

![](img/e7f71c766d941f5a5b9d1fed5d4218ba.png)

4.短语`Hello World!`应该出现在控制台窗口中。按`Enter`或点击红色的`X`关闭窗口，返回 Visual Studio。

![](img/27c8166614f38fdd00aa7762156aa40a.png)

5.通过单击左边浅蓝色的空白处，在`Console.ReadLine();`语句上设置一个断点。点击`Start`再次编译和调试您的控制台应用程序。当您的控制台应用程序在断点处停止时，单击`Continue`继续。按`Enter`或点击红色`X`关闭窗口并返回 Visual Studio。

![](img/db515cbbec8c8860bdfdf1dbc68be048.png)

# 创建 C#。NEO 智能合同并为调试进行设置

6.在 Visual Studio 的解决方案资源管理器中右键单击解决方案名称，然后选择`Add` - > `New Project...`，将 NEO 智能合约项目添加到此解决方案中。

![](img/3f0ac6fe62c6adaccf711a5c982e00be.png)

7.选择`Visual C#`->-`NeoContract`。输入项目名称(如建议使用`NPC.yourname.QuickCycle.Contract`)。点击`OK`创建您的 NEO 智能合同项目。

![](img/80663a00960d13c871430a9134104052.png)

8.在解决方案资源管理器中右键单击`NPC.yourname.QuickCycle.Contract`项目，并选择`Set as Startup Project`使智能合约项目成为解决方案中的默认项目。

![](img/6f92b91f4b3219611381e0a08d47dccf.png)

9.单击 Visual Studio 工具栏上的`Build project`或`Build solution`图标。(或者，您可以在解决方案资源管理器中右键单击项目，并选择`Build`或`Rebuild`)。

![](img/42b3a31d3ef80b3a6ebd04d13a9d1931.png)

10.请注意，在 Visual Studio 左下角的“输出”面板中，您的智能协定已经生成，NEO VM 字节代码(.AVM)文件是由默认(非调试器)版本的 NEO 编译器(`neon.exe`)创建的。

![](img/3080b1abf5909428750d4dd791b1e4d5.png)

11.要更新项目设置以使用 NEO 编译器的调试器版本，并配置 NEO 调试器以在您单击`Start`时启动，请在解决方案资源管理器中右键单击`NPC.yourname.QuickCycle.Contract`项目并选择`Properties`。

![](img/32f4f9d7ed1dad8548422204f2f1b214.png)

12.需要更新两组项目属性:`Build Events`和`Debug`。

点击`Build Events`选项卡。在`Post-build event command line`文本框中，将可以找到 NEO 编译器可执行文件(`neon.exe`)的调试器版本的路径预先附加到`%PATH%`环境变量，然后调用编译器，将 Visual Studio `$(TargetPath)`环境变量的值传递给它。将以下命令复制并粘贴到`Post-build event command line`文本框中。

```
set PATH="C:\repos\neo-debugger-tools\NEO-Compiler\bin\Debug";%PATH%
neon.exe $(TargetPath)
```

![](img/5790e222cd26655833da6ea9a7ac91f4.png)

13.点击`Debug`选项卡。点击`Start external program`单选按钮。在`Start external program`文本框中，输入 NEO 调试器可执行文件的完整路径名(`neod.exe`)。将以下命令复制并粘贴到`Start external program`文本框中。

```
C:\repos\neo-debugger-tools\NEO-Debugger\bin\Debug\neod.exe
```

![](img/be246e87b1fd1346821974107b747a1b.png)

14.在`Command line arguments`文本框中，输入与`.avm`连接的智能合约项目的名称(例如`NPC.yourname.QuickCycle.Contract.avm`)。这是 NEO 编译器在构建过程中使用的默认名称。

![](img/e570dcddbe1765b0d592952556c5c1d5.png)

15.单击项目属性选项卡上的`X`将其关闭。

![](img/8afbd914acf84a35fd86753aee2e2654.png)

16.在解决方案资源管理器中右键单击智能合同项目，并选择`Rebuild`。

![](img/2fc0ac0890b8eab021cffc3abb602832.png)

17.请注意，在`Output`面板中，作为构建过程的一部分，已经执行了两次编译:I)首先运行 NEO 编译器的调试器版本，然后 ii)作为“后期构建”步骤运行 NEO 编译器的非调试器版本。我们需要删除非调试器版本的 NEO 编译器的调用。

![](img/b3438f32c1793d4bb09ee28aab517b62.png)

18.在解决方案资源管理器中右键单击项目并选择`Open Folder in File Explorer`。

![](img/3ee6017c5d5d8920c0f19d7b680bf71e.png)

19.我们需要编辑`NPC.yourname.QuickCycle.Contract.csproj` C#项目文件来删除 post-post-build 步骤。右击 C#项目文件，用 Visual Studio 以外的文本编辑器打开它(例如记事本或 Visual Studio 代码)。

![](img/3f2fdfb6924dc9c945bf54ce5bf6f3ff.png)

20.向下滚动到 C#项目文件的底部。选择以下行，按`Delete`或`Backspace`从 C#项目文件中删除这些行。

```
<Target Name="AfterBuild">
    <Message Text="Start NeoContract converter, Source File: $(TargetPath)" Importance="high">
    </Message>
    <ConvertTask DataSource="$(TargetPath)" />
</Target>
```

![](img/4d15b66ddbc857318f68f831d3033159.png)

21.保存文件并关闭文本编辑器。

**注意**:如果您使用 Visual Studio 代码编辑 C#项目文件，请在关闭 Visual Studio 代码之前仔细检查您是否已经保存了该文件。Visual Studio 代码将为您保存已编辑但未保存的文件。它不会提示您在关闭前检查是否要保存文件。

![](img/adbcdfd93c4774a99749a05305183054.png)

22.当您返回 Visual Studio 时，将出现下面的弹出对话框。点击`Reload`重新加载 C#项目文件。除了移除后期生成事件之外，这对您的解决方案没有任何影响。

![](img/3f81662c1bb72d2f1a86095ad180eeee.png)

23.在解决方案资源管理器中右键单击智能合同项目，并选择`Rebuild`。

![](img/5d5b97e25a98b1388cd33f88f734551f.png)

24.请注意`Output`面板中的 post-post-build 事件已被移除，仅执行了 NEO 编译器的调试器版本。

![](img/80d62c7892451bf2eb098cd6eca88f8c.png)

# 调试 C#。NEO 智能合同

25.要最终调试您的智能合约，请点击`Start`。

![](img/dec989d02f1029a848130cabaef94b16.png)

26.将启动 NEO 调试器。您的智能合约的源代码应该在主面板中可见。

![](img/0a21e450eb626e216d98070410239947.png)

27.您可以按`F5`开始执行您的智能合同(不启用单步执行),也可以按`F10`单步执行您的合同。

对于此任务，按`F10`单步执行您的智能合同。

将出现下面的`Invoke Smart Contract`弹出对话框。因为这个智能契约的`Main`方法不接受任何参数，也不返回任何值，所以只需单击`Debug`来启动您的调试会话。

![](img/e4d52f825db0bf210fd9e3bd3449ee2c.png)

28.按`F10`单步执行您的智能合同。

![](img/2a4d669c91d7036726ce4600409fb8d9.png)

29.继续按`F10`单步执行智能合约，直到完成并出现`Execution finished`弹出窗口。

![](img/0e6224a7f550430a1d9b5973fd56ef37.png)

**注意**:这次调用你的智能合约消耗了 1 点多汽油。这主要归因于智能合约中的单个`Storage.Put()`系统调用。

30.要检查由 NEO 调试器实现的 NEO 存储仿真器的内容，选择调试器工具栏上的`Debug` - > `Storage`。

![](img/5383bf439a369d6566e798d4e4e50dad.png)

31.将出现以下存储弹出对话框。

**注意:**出现在存储弹出对话框中的单个键值对来自智能合约中单个`Storage.Put()`系统调用的执行。

![](img/c2665ed3f1177493b6c9b9f77e90f5b4.png)

32.单击调试器右上角的`X`将其关闭，并返回到 Visual Studio，在这里您可以继续编辑您的智能合约项目。

33.只需在 Visual Studio 中单击`Start`即可恢复编辑-编译-调试循环。

# 参考

*   【快速入门】neo-csharpcoe， [NEO 区块链快速入门指南。来自](https://github.com/mwherman2000/neo-dotnetquickstart/blob/master/README.md)[的. NET 开发者](https://github.com/mwherman2000/neo-dotnetquickstart/blob/master/README.md)https://github . com/mwherman 2000/neo-dotnetquickstart/blob/master/readme . MD