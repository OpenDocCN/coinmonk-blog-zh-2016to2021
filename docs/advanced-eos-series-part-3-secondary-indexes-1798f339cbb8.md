# 高级 EOS 系列—第 3 部分—二级索引

> 原文：<https://medium.com/coinmonks/advanced-eos-series-part-3-secondary-indexes-1798f339cbb8?source=collection_archive---------0----------------------->

![](img/3c23c8e914d726083e4d7b240da33bdf.png)

欢迎回到高级 EOS 开发系列，在这里我将涉及到教程或课程很少涉及的高级技术和功能。本系列的目的是将您作为 EOS 网络上的分布式应用程序开发人员所需的技能整合在一起。每篇文章都是按照难度排序的，所以如果你想要一个总体的概述，我建议你从第一部分开始，一步步往上。这些例子的完整代码可以在 GitHub 上找到[。](https://github.com/MitchPierias/Advanced-EOS-Examples)

[](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/03_Secondary-Indexes) [## MitchPierias/高级-EOS-示例

### 一系列 EOS 合同开发实例。通过创建…为 MitchPierias/Advanced-EOS-Examples 开发做出贡献

github.com](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/03_Secondary-Indexes) 

> 由于这些是高级或扩展的主题，我很危险地假设你已经知道了基础知识，并且正在寻求进一步的知识。出于这个原因，这些文章中共享的代码将只针对所讨论的目的进行精简。

# 主要索引

到目前为止，您应该知道如何使用 struct 在 EOS 中定义多索引表，但是为了完整起见，让我们简要地浏览一下基本的表定义。

## 定义结构

我们将从定义我们的`struct`开始，它需要`primary_key` 函数，这是我们调用`find(KEY)`时使用的索引。现在让我们使用用户`account_name`来获得唯一性，并在下一章介绍一个更通用的方法。

```
// @abi table items i64
struct Item {
  account_name      id;
  string            name;
  uint64_t          attack;
  account_name      owner;

  auto primary_key() const { return id; };
  EOSLIB_SERIALIZE(Item, (id)(name)(attack)(owner));
};
```

## 创建表

现在我们已经勾勒出了我们的结构。我们可以使用 EOS 库中的`multi_index<TABLE_NAME, TABLE_STRUCT>`方法定义我们的多索引表；

```
typedef multi_index<N(items), Item> items_table;
```

在我们的`typedef`之后，我们可以简单地定义一个引用，然后在构造函数中初始化我们的表，或者我们可以根据需要在每个函数中初始化一个引用。

# 次要索引

让我们通过添加一个二级索引来扩展表的功能，以便根据条目各自的所有者来获取条目。我们的`Item`结构现在看起来像这样；

```
// @abi table items i64
struct Item {
  auto              id;
  string            name;
  uint64_t          attack;
  account_name      owner;

  auto primary_key() const { return id; };
  uint64_t get_owner() const { return owner; };
  EOSLIB_SERIALIZE(Item, (id)(name)(attack)(owner));
};
```

我在我们的`primary_key`索引下面添加了一行`unit64_t get_owner() const { return owner; };`。当被调用时，这个函数简单地返回`owner`的值。

## 定义二级索引

让我们将它定义为多索引表中的一个二级索引，这样我们可以在以后访问它；

```
typedef multi_index<N(items), Item, indexed_by<N(byowner), const_mem_fun<Item, uint64_t, &Item::get_owner>>> item_table;
```

不要让这一行吓到你，这只是我们之前的声明加上一个额外的参数。

```
indexed_by<N(byowner), const_mem_fun<Item, uint64_t, &Item::get_owner>>
```

这是定义我们的二级索引的线。我们使用`indexed_by<INDEX_NAME, LOOKUP_FUNCTION_DEFINITION>`来指定名为`byowner`的索引。

*   INDEX _ NAME 可以是您喜欢的任何名称，并且不需要在语法上与我们的函数`get_owner`匹配，只要记住它是我们将在稍后的操作中用来访问二级索引的名称。
*   LOOKUP_FUNCTION_DEFINITION:在我们的示例中类似于`cons_mem_fun<STRUCT, RETURN_VALUE, LOOKUP_FUNCTION>`,构造我们的二级索引函数，并将其分配给我们的索引定义函数。

## 使用二级索引

所以我们已经为我们的商品的`owner`建立了索引，但是我们如何使用我们的索引来查找特定用户拥有的商品呢？为此，我们将进入一个操作，并使用多索引表方法`get_index<INDEX_NAME>()`，让我们创建一个名为`inventory`的操作来获取用户条目。

```
// @abi action
void inventory(const account_name account) {
  item_table items(_self, _self);
  auto playerItems = items.get_index<N(byowner)>();
  auto iter = playerItems.lower_bound(account);
  while (iter != playerItems.end()) {
    print("Item ", iter->name);
    iter++;
  }
}
```

让我们打开下面的代码行，更深入地研究它负责什么；

```
auto playerItems = items.get_index<N(byowner)>();
```

这一行使用我们的`item_table`实例的`get_index`函数来获取我们之前定义的索引。注意，我们使用了之前指定的相同名称`N(byowner)`来引用我们正在寻找的索引。

这是二级索引，现在我们有了索引，我们可以执行所有我们通常用 primary_index 做的功能。在本例中，我们使用的是`lower_bound`，但是您也可以使用`find`、`get`、`upper_bound`、`begin`或`end`来适应您的特定用例。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)

[**Click to read today’s top story**](http://bit.ly/2G71Sp7)