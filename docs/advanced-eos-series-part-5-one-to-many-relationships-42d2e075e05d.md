# 高级 EOS 系列—第 5 部分—一对多关系

> 原文：<https://medium.com/coinmonks/advanced-eos-series-part-5-one-to-many-relationships-42d2e075e05d?source=collection_archive---------1----------------------->

![](img/04daff84a451d3e9c9f6d2ff451fdb97.png)

欢迎回到高级 EOS 开发系列，在这里我将涉及到教程或课程很少涉及的高级技术和功能。本系列的目的是将您作为 EOS 网络上的分布式应用程序开发人员所需的技能整合在一起。每篇文章都是按照难度排序的，所以如果你想要一个总体的概述，我建议你从第一部分开始，一步步往上。这些例子的完整代码可以在 GitHub 上找到[。](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/05_Table-One-To-Many-Relationships)

[](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/05_Table-One-To-Many-Relationships) [## MitchPierias/高级-EOS-示例

### 一系列 EOS 合同开发实例。通过创建…为 MitchPierias/Advanced-EOS-Examples 开发做出贡献

github.com](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/05_Table-One-To-Many-Relationships) 

> 由于这些是高级或扩展的主题，我很危险地假设你已经知道了基础知识，并且正在寻求进一步的知识。出于这个原因，这些文章中共享的代码将简明扼要地达到所讨论的目的。

这篇文章相对来说比较短，但是涵盖了一个基本的，看起来没有涉及到的方法，多索引表，唯一的主索引。

# 一对多关系

我来自一个有图形数据库背景的人，这是一个奇妙的地方，数据也是直接链接的，就像 MySQL joins 一样，但是是类固醇。因此，如果你像我一样，已经掌握了 EOS 表的基本知识，那么你可能会问这样一个问题，但是我如何在多个表和行之间创建关系呢？在这个例子中，我们将研究使用 EOS `multi_index`表创建一对多关系的多种方法，而不是一种。

在这些例子中，我们将看看三种不同的方法来创建我们的 EOS 表行之间的关系；向量、索引和范围。

> 为了简单起见，下面的例子将不验证调用者帐户。

# 向量

[c++中的 Vectors](http://www.cplusplus.com/reference/vector/vector/)表示动态数组的结构，允许我们存储任意大小的值集合。让我们通过存储玩家`Profile`的唯一`Item`标识符数组来探索向量。这里，存储在`Profile`表中的标识符将对应于`Item`表中行的`id`。

```
struct Profile {
  name              account;
  vector<uint64_t>  items;
}

struct Item {
  uint64_t          id;
  string	            name;
}
```

现在，在我们的`create()`方法中，我们可以简单地使用之前存储的`Item.id`和 vector 类的`push_back()`方法来推送对`Item`的引用。

```
owners.modify(currentPlayer, 0, [&](auto& owner) {
  owner.items.push_back(itemID);
});
```

我们甚至可以更进一步，在我们的项目向量`vector<uint64_t, uint32_t> items;`中定义一个子集合，其中`uint64_t`是我们的项目标识符，`uint32_t`是项目的年龄。或者，一个二级结构(集合)可以存储在 vector 中，如`vector<Item> items;`。

向量是在我们的表中直接存储相关数据和引用的一种很好的方式，但是由于过多的用户输入或不正确的数据管理，表行可能会很快变大。让我们转而研究二级索引来创建表之间的关系。

# 次要索引

使用二级索引提供了另一种方法，我们可以缩放引用，同时避免表行中过大的数组*(向量)*。它还允许我们通过使用从子到父的向后引用，在表中的父子行之间无缝移动。让我们从以前的 Vector 部分扩展我们的项目，来实际地看看这个。首先我们要修改我们的结构，从`Profile`结构中移除`items`向量，并在`Item`结构中添加对我们所有者的引用。

```
struct Profile {
    name 	account;
}

struct Item {
    uint64_t	uid;
    string	name;
    name		owner;
}
```

现在我们需要添加搜索功能，这样我们就可以通过`owner`找到我们的`Item`。让我们创建一个二级索引，并将其添加到我们的`multi_index`定义中。

```
struct Item {
    ...
    uint64_t get_owner() const { return owner; }
}

typedef multi_index<N(items), Item, indexed_by<N(byowner), const_mem_fun<Item, uint64_t, &Item::get_owner>> item_table;
```

其中我们的索引名将是`byowner`，它将使用`get_owner`函数返回`uint64_t owner`键。

> multi_index 方法允许我们为每个表定义多达 16 个附加索引。

我们将不再从添加项目功能中更新我们的`Player`。相反，我们将在新创建的项目的`owner`属性中存储对签名人`account`的引用。让我们更新我们的`additem`行动来实现这一点；

```
items.emplace(account, [&](auto& item) {
    ...
    item.owner = account;
});
```

现在我们可以像这样得到一个玩家的所有物品；

```
void indexes::get(const name account) {
  item_table items(_self, _self);
  auto accounts_items = items.get_index<N(byowner)>();
  auto iter = accounts_items.lower_bound(account);
  while (iter != accounts_items.end(); iter++) { // Do stuff }
}
```

# 范围

对于最后一个方法，我们将使用表范围来表示表行之间的关系。使用作用域增加了一种受保护的安全性，即只有在作用域已知的情况下才能找到数据。

## EOS 数据结构

```
*- code       * The account name assigned write permission (contract)
-- scope     * The account where the data is stored
--- table    * Name of the table being stored
---- record  * A table row*
```

我们将修改我们的`Item`来利用表的范围来查找所有玩家的物品。为了简化我们的项目，我们将从我们的`Item` struct 和 multi_index 表定义中移除`owner`引用和`get_owner()`索引。

```
struct Item {
    uint64_t    	id;
    string	name;
    auto primary_key() const { return id; };
    EOSLIB_SERIALIZE(Item, (id)(name));
};

typedef multi_index<N(items), Item> item_table;
```

真正的魔力发生在我们的 setters 和 getters 中，让我们看看它们是如何变化的。

```
item_table items(_self, account);
auto item = items.emplace(account, [&](auto& item) {
  ... configure item
});
```

如果您查看我们的实例化表，您会注意到我们现在使用的是我们的`account`的范围，而不是契约的`code`本身。我们的 get 操作也以同样的方式进行了修改。

```
item_table items(_self, account);
auto iter = playerItems.lower_bound();
while (iter != playerItems.end()) { ... }
```

就这么简单！使用范围隔离数据使得查找与用户相关的数据变得更加简单，然而，像计算所有用户的所有项目这样的事情变得更加困难。选择适用于您的应用目标的方法非常重要。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)