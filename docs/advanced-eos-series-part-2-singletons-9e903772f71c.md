# 高级 EOS 系列—第 2 部分—单件

> 原文：<https://medium.com/coinmonks/advanced-eos-series-part-2-singletons-9e903772f71c?source=collection_archive---------4----------------------->

![](img/7f9f8579798f603d9fda1694f94a5684.png)

欢迎来到高级 EOS 开发系列，在这里我将涉及到教程和课程很少涉及的技术和功能。本系列的目的是将您作为应用程序开发人员在 EOS 网络上完成技能所需的缺失部分汇集在一起。每篇文章都是按照难度排序的，所以如果你想要一个总体的概述，我建议你从第 1 部分开始，一步步往上。对于本系列的第 2 部分，我们将着眼于单例，这个例子的所有代码都可以在 GitHub 上找到[。](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/02_Singletons)

[](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/02_Singletons) [## MitchPierias/高级-EOS-示例

### 一系列 EOS 合同开发实例。通过创建…为 MitchPierias/Advanced-EOS-Examples 开发做出贡献

github.com](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/02_Singletons) 

> 由于这些是高级或扩展的主题，我很危险地假设你已经知道了基础知识，并且正在寻求进一步的知识。因此，这些文章中共享的代码将针对所讨论的主题进行精简。

> 单例应该用于存储契约状态，或者在只需要一行时作为多索引表的替代。

## 坚持

我们将探索单例作为一种持久化契约状态的方法。鉴于我们的数据只会占用一行，为此使用表将是对资源的浪费。

## 定义单例

对于这个例子，我们将使用一个单例来存储我们的合同配置状态。让我们用一个结构体来定义我们的单例。我们将使用一个名为`closed` 和`char_count`的布尔值来限制用户的帖子大小。

```
struct Config {
  bool		closed = false;
  uint32_t	char_count = 144;
};

typedef singleton<N(settings), Config> settings_table;
```

接下来，我们将为实例创建一个占位符变量，使它在我们的契约上下文中是全局的。

```
settings_table config;
```

## 初始化单例

我们可以在每次使用 singleton 时或者在我们的 contracts 构造函数中初始化它，这样它在我们的 contract 的每个动作中都是可用的。我们的合同在这种情况下被命名为`singletons`，但你可以叫它什么。

```
singletons(action_name self) : contract(self), config(_self, _self) {}
```

类似于多索引表，初始化单例需要契约代码和范围。在这个例子中，我们将代码和范围都设置为`_self.`

## 获取单一值

既然我们已经初始化了单例，我们可以通过读取我们的`char_count`值并将其打印到控制台来开始与值交互。

```
auto state = config.get();
print(state.char_count);
```

就是这样！我们的`config.get()`检索我们之前初始化的单例，并将其赋给我们的`state`变量。现在我们可以使用点符号来访问我们的`struct`的属性。

## 设置单独值

在这个例子中，我们将使用成员函数`singleton.set(STRUCT,PAYER).` 更新`char_count`属性。让我们看两个方法来更新我们的 singleton。

**方法 1 —内嵌更新**

最简单的方法是内联构建我们的 struct，在我们设置它的时候直接把它传递给我们的 set 函数，就像这样；

```
config.set(Config{true, 172}, _self);
```

这里我们将`closed`设为 true，将`char_count` 设为 172，同时委托`_self`作为这个 RAM 的付款人。当你的结构只包含几个属性时，这个方法是干净的，并且保持属性的顺序，但是对于更大的结构，我们需要一个更好的方法。

**方法 2 —检索和更新**

接下来，让我们使用获取和更新方法重新打开我们的契约。首先，我们将使用`singleton.get()`方法获取当前的`Config`状态，设置`closed`值，然后再次设置状态。

```
auto state = config.get();
state.closed = false;
config.set(state, _self);
```

这就是单身，简单！在下一部分中，我们将研究多索引表索引，主要关注使用二级索引。如果您想在我分享更多示例时得到通知，请确保点击*关注*。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)

[**Click to read today’s top story**](http://bit.ly/2G71Sp7)