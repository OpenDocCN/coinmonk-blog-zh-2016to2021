# 尽量减少智能合同中的汽油用量

> 原文：<https://medium.com/coinmonks/minimize-gas-in-your-smart-contracts-bb9a89db1052?source=collection_archive---------5----------------------->

## 简单的技巧可以减少你为智能合同支付的油费

**在参加了一个关于可靠性发展的课程后，我从支付过多的汽油费用变成了支付合理的费用。**

![](img/a767e470b4137a8a874fdd9c59c09d80.png)

Photo by [Jess Bailey](https://unsplash.com/@jessbaileydesigns?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我会在这里告诉你主要的诀窍，这样你就不会浪费时间去完成整个[课程](https://cryptozombies.io/)。

# 1.较小的单位

如果在一个结构中有多个 uint，那么使用一个小的 uint。这允许 Solidity 使用更少的存储空间。

转换这个:

```
 struct NormalStruct {
 uint a;
 uint b;
 uint c;
}
```

对此:

```
struct MiniMe {
 uint32 a;
 uint32 b;
 uint c;
}
```

由于结构包装，比“正常结构”消耗更少的气体

# 2.查看功能不会让您付出任何代价

当用户从外部调用视图函数时，它们不会消耗任何资源。

这是因为视图函数实际上不会改变区块链上的任何内容，它们只是读取数据。

尽可能使用视图功能。这里有一个例子:

```
# [https://github.com/spiyer99/CryptoZombies/blob/master/contracts/zombie_helper.sol#L36](https://github.com/spiyer99/CryptoZombies/blob/master/contracts/zombie_helper.sol#L36)function getZombiesByOwner(address _owner) external view returns(uint[] memory) {
  uint[] memory result = new uint[](ownerZombieCount[_owner]);
  uint counter = 0;
  for (uint i = 0; i < zombies.length; i++) {
    if (zombieToOwner[i] == _owner) {
      result[counter] = i;
      counter++;
    }
  }
  return result;
}
```

这个功能可以让我们找到某人拥有的所有僵尸。

我们通过遍历每个僵尸来做到这一点。

是的，这太天真了。我们可以简单地创建一个散列图。这将把查找减少到 O(1)。

但是它也比散列图便宜。所以更好。结束了。

# 结论

就是这样！这两件事为我节省了相当多的汽油。希望他们也能帮到你。