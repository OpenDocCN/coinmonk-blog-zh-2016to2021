# 高级 EOS 系列—第 4 部分—表格唯一性

> 原文：<https://medium.com/coinmonks/advanced-eos-series-part-4-table-uniqueness-835843a207fc?source=collection_archive---------0----------------------->

![](img/7872299622326cfe6c53348fb8aba86a.png)

欢迎回到高级 EOS 开发系列，在这里我将涉及到教程或课程很少涉及的高级技术和功能。本系列的目的是将您作为 EOS 网络上的分布式应用程序开发人员所需的技能整合在一起。每篇文章都是按照难度排序的，所以如果你想要一个总体的概述，我建议你从第 1 部分开始，一步步往上。这些例子的完整代码可以在 GitHub 上找到[。](https://github.com/MitchPierias/Advanced-EOS-Examples)

[](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/04_Table-Uniqueness) [## MitchPierias/高级-EOS-示例

### 一系列 EOS 合同开发实例。通过创建…为 MitchPierias/Advanced-EOS-Examples 开发做出贡献

github.com](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/04_Table-Uniqueness) 

> 由于这些是高级或扩展的主题，我很危险地假设你已经知道了基础知识，并且正在寻求进一步的知识。出于这个原因，这些文章中共享的代码将只针对所讨论的目的进行精简。

这篇文章相对来说比较短，但是涵盖了一个基本的，看起来没有涉及到的方法，多索引表，唯一的主索引。

# 唯一索引

到目前为止，我们已经能够在表中创建由唯一的`account_name`标识符索引的行，但是我们如何使用不同的`primary_key`并确保它是唯一的呢？幸运的是 EOSio 的开发者为我们提供了`available_primary_key()`功能。

让我们从创建一个结构来定义我们的`items`表中的`Item`开始。我们将使用一个普通的`uint64_t`来指定我们的主键`id`。

```
// @abi table items i64
struct Item {
  uint64_t          id;
  string            name;
  uint64_t          attack;
  account_name      owner;

  auto primary_key() const { return id; };
  EOSLIB_SERIALIZE(Item, (id)(name)(attack));
};

typedef multi_index<N(items), Item> items_table;
```

你会注意到这是我们在[高级 EOS 系列—第 3 部分—二级索引](/coinmonks/advanced-eos-series-part-3-secondary-indexes-1798f339cbb8)中使用的相同的表结构。现在让我们更新我们的操作来生成一个惟一的主键，而不是要求它们作为一个参数。

```
void inventory::create(const account_name account, const string name, const uint64_t attack) {

  items_table items(_self, _self);

  items.emplace(account, account, [&](auto& item) {
    item.id = items.available_primary_key();
    item.name = name;
    item.attack = attack;
    item.owner = account;
  });
}
```

我们已经从我们的动作`void post(const account_name username, uint64_t index const string& msg_str)`中移除了`uint64_t index`，并且我们已经用`items.available_primary_key()`替换了`index`，以便在我们创建新项目时设置主键`id`。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)