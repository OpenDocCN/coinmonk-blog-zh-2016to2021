# 高级 EOS 系列—第 6 部分—合同间通信

> 原文：<https://medium.com/coinmonks/advanced-eos-series-part-6-contract-to-contract-communication-ab352a8b60aa?source=collection_archive---------1----------------------->

![](img/27363975906b0585a7c487a309ec17b6.png)

欢迎回到高级 EOS 开发系列，在这里我将触及教程或课程很少涉及的技术和功能。本系列的目的是将您需要完成的作为 EOSIO 区块链分布式应用程序开发人员的技能的缺失部分汇集在一起。每篇文章都是按照难度排序的，所以如果你想要一个总体的概述，我建议你从第 1 部分开始，一步步往上。这些例子的完整代码可以在 GitHub 上找到[。](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/06_Contract-to-Contract-Communication)

[](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/06_Contract-to-Contract-Communication) [## MitchPierias/高级-EOS-示例

### 一系列 EOS 合同开发实例。通过创建…为 MitchPierias/Advanced-EOS-Examples 开发做出贡献

github.com](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/06_Contract-to-Contract-Communication) 

> 由于这些都是高级主题，我很危险地假设你已经知道了基础知识，并正在寻求进一步的知识。因此，这些文章中共享的代码将针对所讨论的主题进行精简。

# 在我们开始之前…

执行内嵌操作需要自定义帐户权限。出于安全原因，EOS 的发布版本现在需要调用内联动作的契约的`eosio.code`权限。让我们看看如何为我们的合同帐户创建自定义权限。

## **修改账户权限**

在调用对我们的`user`契约的操作之前，我们修改我们的`timeline`帐户的权限。我们需要`eosio.code`权限来调用我们的内联动作，所以我们将在调用动作的帐户上覆盖我们的`active`键。让我们使用`cleos set account permission`命令来实现这一点。

```
cleos set account permission timeline active
'{
  "threshold":1,
  "keys":[{
    "key":"YOUR_PUBLIC_KEY",
    "weight":1
  }],
  "accounts":[{
    "permission":{
      "actor":"timeline",
      "permission":"eosio.code"
    },"weight":1
  }]
}'
owner -p timeline@owner
```

现在我们已经修改了我们的权限，我们可以仔细看看在一个契约和另一个契约之间调用动作的`eosio::action`方法。

## 用户合同

首先，我们将编写一个`user`契约来管理用户的注册、激活和验证。让我们定义我们的用户`Profile`结构和一个`create`动作来注册新用户。我们还将快速添加一个`activate`动作来改变用户的`active`状态。记得初始化您的契约并序列化您的 ABI 操作。

```
// @abi action
void create(const account_name username) {
  profile_table profiles(_self, _self);
  auto result = profiles.find(username);
  eosio_assert(result == profiles.end(), "User already registered");
  // Store new user profile
  profiles.emplace(_self, [&](auto& profile) {
    profile.uuid = username;
  });
}// @abi action
void activate(const account_name username, bool isActive) {
  profile_table profiles(_self, _self);
  auto results = profiles.find(username);
  profiles.modify(results, 0, [&](auto& profile) {
    profile.active = isActive;
  });
}// @abi table profiles i64
struct Profile {
  uint64_t      uuid;
  bool          active = true;
  bool          approved = true;
  bool          flagged = false;
  auto primary_key() const { return uuid; }
  EOSLIB_SERIALIZE(Profile, (uuid)(active)(approved)(flagged));                         };typedef multi_index<N(profiles), Profile> profile_table;
```

我们还将编写一个 validate 方法来检查指定的用户是否通过了我们所有的验证要求。为此，我们将获取用户的个人资料，并检查他们的活动状态，如果他们已经被标记*(警告)*，如果他们的帐户被批准。

```
// @abi action
void validate(const account_name username) {
  profile_table profiles(_self, _self);
  auto results = profiles.find(username);
  eosio_assert(results->active, "User inactive");
  eosio_assert(!results->flagged, "User flagged");
  eosio_assert(results->approved, "User not approved");                         }
```

## 时间表合同

现在我们要写一个`timeline`契约来处理发布和删除帖子。为了保持这篇文章的简洁，我将只涉及出版方面。让我们定义`Tweet`结构来存储我们发布的 tweets。

```
// @abi table tweets i64
struct Tweet {
  uint64_t       id;
  account_name   author;
  string         msg;
  bool           flagged;
  auto primary_key() const { return id; }
  EOSLIB_SERIALIZE(Tweet, (id)(author)(msg)(flagged));
};typedef multi_index<N(tweets), Tweet> tweet_table;
```

## 合同间通信

当我们试图用我们的`timeline`合同`publish`一条 tweet 时，我们的合同间通信将会发生。在存储推文之前，我们需要验证用户的帐户。让我们使用`action(PERMISSIONS,TO_ACCOUNT,TO_ACTION,DATA_TUPLE).send()`方法调用`user`合同上的`validate`动作。

```
// @abi action
void publish(const account_name username, const string& msg_str) {

  eosio::action(
    permission_level{_self, N(active)},
    N(user),
    N(validate),
    std::make_tuple(username)
  ).send(); tweet_table tweets(_self, _self);
  tweets.emplace(_self, [&](auto& obj) {
    obj.id = tweets.available_primary_key();
    obj.author = username;
    obj.msg = msg_str;
  });
}
```

就这么简单！只要你已经正确设置了合同帐户的`eosio.code`权限，你就可以调用另一个合同的动作。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)