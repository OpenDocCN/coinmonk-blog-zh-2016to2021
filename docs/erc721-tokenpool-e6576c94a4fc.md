# ERC721 令牌池

> 原文：<https://medium.com/coinmonks/erc721-tokenpool-e6576c94a4fc?source=collection_archive---------8----------------------->

`TokenPool`是具有 4 级池结构的`TokenContainer`:

```
Level     Container / Member
--------------------------------------------
  0       SuperPool
  1           |_____.Pool
  2                   |_____.SubPool
  3                             |_____.Token
```

由以下结构描述的池模式:

```
struct Pool {
        uint8   level;      // Pool level: 0,1,2,3
        uint256 maxNumber;  // Maximum number of pools on this lavel
        uint256 maxMember;  // Maximum number of members for the pool
        uint256 number;     // Pool number for this level
        uint256 last;       // NFT ID for last availible pool (with member capacity)
        uint256 share;      // Pool share from token investment
 }
```

使用`insertPool`方法在池结构中插入令牌:

```
function insertPool(uint256 _id) public returns(bool);
```

该函数调用`_insertPool`来插入令牌，如果需要，还可以在结构中插入新的池:

```
function _insertPool(uint256 _id, uint8 _level) internal returns (bool);
```

在令牌被插入到池结构中之后，函数`insertPool`调用值分布函数:

```
function _distributeValue(uint256 _id) internal returns (bool);
```

然后根据`Pool.share`在`SuperPool`、`Pool`和`SubPool`之间分配代币值。池分配后剩余的代币价值将用于佣金。使用以下方法获取收集的佣金和当前池值:

```
function getComission() public view returns(uint256 commission);function getDistribution() public view returns(uint256[4] distribution)
```

最后一个函数返回以下值:

```
distribution[0];         // Super Pool Value
distribution[1];         // Pool Value
distribution[2];         // SubPool Value
distribution[3];         // Tokens Value (must be 0)
```

为了支付价值，智能合约使用以下函数:

```
function _payValue(uint256 _id, uint256 _value) internal returns(uint256[4] distribution);
```

`__payValue`基于与`getDistribution`相同的结构返回支付的分布，但是在这种情况下，只有一个数组元素可以具有等于`_value`的非零值。例如，如果 Pool 已经付款，那么`distribution[1] == _value`和剩余的`distribution[0,2,3] == 0`。如果所有元素的值都为零，则支付不成功，并且所有池都没有足够的值来支付请求的金额。在这种情况下，`_payValue`发出`SecondTierCall`事件。如果支付成功，则发出事件`PaymentValue`:

```
event PaymentValue(uint256 id, uint256 value, uint8 level);
```

它将返回已付款的池级别，在我们的示例中是`level == 1`。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)