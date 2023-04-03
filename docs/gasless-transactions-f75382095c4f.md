# 无汽油或元交易

> 原文：<https://medium.com/coinmonks/gasless-transactions-f75382095c4f?source=collection_archive---------0----------------------->

![](img/2bf9cf11975b13ae9d1f2900c889d638.png)

Gas can lead to UX problems, “Gasless” transactions may hold the solution.

{这篇文章是上一篇文章[的延续。](/coinmonks/crypto-ux-and-key-management-6e35b3cd466d) }

气体的概念对于大多数非技术用户来说是陌生的。然而，支付交易费的想法是不可行的。许多传统的实体银行[对每笔借记卡交易收取固定的交易费用，或者将这些费用计入固定的季度“维护”费用。事实上，这就是人们所认为的 UX 问题，](https://www.ratehub.ca/chequing-accounts/accounts/personal)[一些连锁店](https://cryptobriefing.com/eos-ethereum-smart-contract-war-winner/)已经转向相反的模式，即执行合同支付天然气费用，而不是交易发送方。

天然气成本可通过以下等式转化为货币成本:成本=美元/天然气价格*天然气数量。天然气数量取决于交易的复杂程度，天然气价格是一个[用户选择的值](https://ethgasstation.info/index.php)，该值通常规定交易需要多长时间才能包含在区块中。一天中的时间对网络拥塞程度有很大影响，因此，如果交易不紧急，则可以设置低的天然气价格，并且该交易可能最终被包括在内。

没有任何关联以太网的密钥对不能直接在以太网上发送交易，因为它们没有支付汽油费用的能力，但不用担心，已经设计了几种机制来回避这个问题。其中一些机制很好地结合了一次性密钥的概念，之前在这里讨论过。签署符合 [ERC-191](https://github.com/ethereum/EIPs/issues/191) 的事务并将该事务发送到富含以太/气体的节点的能力是一个强大的概念，该节点然后可以将该签署的事务广播到网络。通过支付一些 ERC-20 代币，甚至复制传统的“维护”收费模式(用户为交易支付特定的季度费用)，可以从经济上激励这个中继器广播这些交易。

**基于身份的气体继电器**

Status[identitygasrelay . sol](https://github.com/status-im/contracts/blob/73-economic-abstraction/contracts/identity/IdentityGasRelay.sol)中描述了其中一个无气交易模型。该合同继承了 [Identity.sol](https://github.com/status-im/contracts/blob/73-economic-abstraction/contracts/identity/Identity.sol) ，在[上一篇文章](/coinmonks/crypto-ux-and-key-management-6e35b3cd466d)中有详细分析。从 Identity.sol 继承的`nonce`字段对照传递的`_nonce`进行检查并递增，以保持对重放攻击的抵抗，并确保事务与身份契约同步。

`MANAGEMENT`和`ACTION`密钥用于确保交易签名人有足够的权限来执行所请求的交易，而`purposeThreshold`映射用于确保已达到足够数量的签名人用于该密钥目的。`isKeyPurpose()`，在 Identity.sol 中广泛使用，再次用于确保密钥达到预期目的。类似于身份契约，它旨在为每个用户部署该契约的一个单独实例(这与 uPort 的 [ERC-1056](https://github.com/ethereum/EIPs/issues/1056) 轻量级身份提议形成对比)。

**工作原理**

该气体继电器的主要功能通过两个主要功能实现:`callGasRelayed(...)`和`approveAndCallGasRelayed(...)`。这些功能的总体结构是相似的:

1.  执行健全性检查
2.  计算应该签名的散列值
3.  确保签名是正确的
4.  发送事务，向观察者发出确认事件
5.  适当地退款给打电话的人

这两个`callGasRelayed(...)`和`approveAndCallGasRelayed(...)`采用相似的参数:要中继的交易目的地地址、交易值、交易数据、当前 nonce、要退还给该函数调用方的汽油价格。这些功能旨在由“汽油丰富”的继电器调用，并且这些功能中内置了这样的逻辑，以乙醚的形式或者如果指定的话，以某种类型的 ERC-20 令牌的形式向调用者退还汽油费用。

这两个外部可调用的函数都从执行一些健全性和安全性检查开始:确保函数调用与预期使用的量相比有足够的 gas，确保`nonce’s`匹配，以及在`approveAndCallRelayed`的情况下，退款 ERC-20 令牌地址是否有效。

然后计算出`signHash`。这是所传递参数的 keccak 散列，符合 ERC-191: `signHash = keccak256(“\x19Ethereum Signed Message:\n32”, _hash);`。这个`signHash`将用于确保交易由适当的密钥签名。

这两个函数的主要区别在于执行检查以确保签名密钥具有正确的权限。`callGasRelayed()` 允许签名来自`MANAGEMENT`或`ACTION`键:

```
// callGasRelayed(...)
...
verifySignatures( 
    _to == address(this) ? MANAGEMENT_KEY : ACTION_KEY,
    signHash,
    _messageSignatures 
);
...
```

`callGasRelayed`然后继续执行一个低级调用:`_to.call.value(_value)(_data)`的事务，并发出一个`ExecutedGasRelayed`事件，表示该调用成功。相反，`approveAndCallGasRelayed`不允许使用`MANAGEMENT`键:

```
// approveAndCallGasRelayed(...)
...
verifySignatures(
    ACTION_KEY, //no management with approveAndCall
    signHash,
   _messageSignatures
);
...
```

**验证签名**

`verifySignatures()`有`view`修饰符，它允许函数中的代码读取合同状态，但无论如何不能修改它。像`view`和`pure`这样的内置修饰符[被认为是最佳实践](https://ethereum.stackexchange.com/questions/28898/when-to-use-view-and-pure-in-place-of-constant)，很好地反映了 C++的封装原则——尽可能地限制状态修改能力。

```
function verifySignatures(uint256 _requiredKey,
bytes32 _signHash,
bytes _messageSignatures )

public view returns(bool) 
{ 
  uint _amountSignatures = _messageSignatures.length / 72;
  require(_amountSignatures == purposeThreshold[_requiredKey]);
  bytes32 _lastKey = 0;
  for (uint256 i = 0; i < _amountSignatures; i++) 
  { 
   bytes32 _currentKey = recoverKey(_signHash,_messageSignatures,i);
   require(_currentKey > _lastKey); //assert keys are different 
   require(isKeyPurpose(_currentKey, _requiredKey));
   _lastKey = _currentKey;
  }
  return true;
}
```

多个签名作为`_messageSignatures`被传递给`verifySignatures()`，签名的数量通过除以 72 字节(单个签名的预期大小= 32 + 32 + 1)确定，并连同之前计算的`signHash`和`_messageSignatures`字节变量内的签名索引一起被发送给`recoverKey()`。

```
function recoverKey (bytes32 _signHash,
bytes _messageSignature,
uint256 _pos)pure public returns(bytes32)
{
  uint8 v;
  bytes32 r;
  bytes32 s;
  (v,r,s) = signatureSplit(_messageSignature, _pos);
  return bytes32(ecrecover(_signHash,v,r,s));
}
```

`signatureSplit()`获取字节 blob 和一个索引`pos`(循环迭代器)，并使用内联汇编提取签名的`v`、`r`和`s`组件。内联汇编在这里对于细粒度提取编码在`_signatures`字节中的信息是必要的，回想一下 EVM 是一个基于 256 位堆栈的虚拟机，因此默认为 32 字节的字。签名格式是`{bytes32 r}{bytes32 s}{uint8 v}`的紧凑形式。值`r`和`s` 提取起来很简单——索引乘以 32 字节的偏移量，这个索引偏移量被加到指向`_signatures`开头的指针上。这里很好地解释了这种使用内联汇编提取值的技术[。](https://blog.colony.io/parameterized-transaction-reviews-11f0cdc40479)

紧凑意味着，`uint8`没有填充到 32 字节，这使得提取`v`变得有点棘手。此外，EVM 指令集中没有`mload8`操作码来做这件事。相反，我们加载最后 32 个字节，包括 31 个字节的`s`，并使用`and`和`0xff`(十进制 255)来提取最后一个字节。

```
// signatureSplit(...)
...
assembly {            
  r := mload(add(_signatures, mul(32,pos)));
  s := mload(add(_signatures, mul(64,pos)));
  v := and(mload(add(_signatures, mul(65,pos))), 0xff);
}
```

`ecrecover()`是一个存在于[预编译契约](https://github.com/ethereum/go-ethereum/blob/master/core/vm/contracts.go#L42)中的函数，自 Frontier 发布以来一直可用。等效功能可以用[实度](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/v1.12.0/contracts/ECRecovery.sol)表示。该函数采用 [EC](https://hackernoon.com/a-closer-look-at-ethereum-signatures-5784c14abecc) 签名的`v`、`r`和`s`参数以及这些参数所代表的消息的散列，并从签名密钥对中返回对应于公钥的地址。要求`v` 等于 27 或 28。该参数有助于缓解[特定重放攻击](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-155.md)。

在`verifySignature()`中备份，从`ecrecover()`返回的地址与`require(isKeyPurpose(_currentKey, _requiredKey))`动作所需的密钥进行核对。因此，签名检查控制流看起来像这样:

```
[callGasRelayed() or approveAndCallGasRelayed()] => verifySignatures() =>
forEach(signature){
=> recoverKey() => splitSignature() => ecrecover() => isKeyPurpose()
}
```

**经济激励**

在`nonce`递增、预期值和数据被发送、或 ERC-20 令牌传输被批准、以及`ExecutedGasRelayed`事件被发出之后，是时候对被调用的函数(面向 gas 的账户)进行经济奖励了:

```
...
if (_gasPrice > 0) {
  uint256 _amount = 21000 + (startGas - gasleft());
  _amount = _amount * _gasPrice;
  if (_gasToken == address(0)) { // if no token specified send Ether
    address(msg.sender).transfer(_amount); 
  }
  else { // if token specified then 
    ERC20Token(_gasToken).transfer(msg.sender, _amount);            
  }
}
```

这部分代码可以根据经济上的考虑进行配置。例如，如果中继站可以选择乙醚或通过 ERC-20 令牌批准退款，那么它可能希望检查它正在接收的 ERC-20 令牌的价值是否足够，并根据此信息做出决定。这最有可能在链外完成，因为对 oracle 合同的调用可能无法证明天然气使用量的增加是合理的，但图灵完整语言的可能性和灵活性在这里是显而易见的。正如本帖介绍中提到的，传统的交易费用模型可能在这里实现。继电器可以在单独的合同中维护余额的映射，并相应地减少或增加。

**分析**

这种架构功能强大且灵活，允许任意执行事务，但也存在一些问题:

*   *审查*:交易必须以某种方式到达中继器。因为这是在链外完成的，所以有信任的假设，即富气继电器将按预期运行，并且不会增加对该交易的审查。这个问题并不是这种情况下独有的，交易审查在区块链是一个相对未解决的问题。像这样的“网络边缘”问题是很难有效解决的。
*   *签名持久性*:这种架构要求该动作所需的所有签名都出现在对`callGasRelayed`或`approveAndGasRelayed`的单个调用中。一个稍微灵活一点的设置是在被中继的调用之间有一些持久性，尽管这可以通过一个更迂回的方法来建立，通过使用`IdentityGasRelay.sol`来调用`Identity.sol`中的持久性函数。
*   *签名排序*:出于某种原因，也许是为了加强简洁性，降低燃气成本，签名[必须以升序](https://github.com/status-im/contracts/blob/73-economic-abstraction/contracts/identity/IdentityGasRelay.sol#L188)发送，这降低了合同的灵活性。

向为[元交易](https://metatx.io/)空间做出重大贡献的[奥斯汀·格里菲斯](https://twitter.com/austingriffith)致敬。

在媒体上关注我或在推特上联系我:[https://twitter.com/gawnieg](https://twitter.com/gawnieg)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)