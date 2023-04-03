# 加密货币 UX 和密钥管理

> 原文：<https://medium.com/coinmonks/crypto-ux-and-key-management-6e35b3cd466d?source=collection_archive---------8----------------------->

![](img/0ea825af40d213393e0b55c7116f0c0c.png)

用户体验是大规模采用加密货币的最大障碍之一。与 Whatsapp 在许多国家突然成为主导的安全消息应用程序类似，加密货币领域也有机会通过简化用户体验获得大规模采用。包含在 UX 这个刻意宽泛的话题中的有:

*   入职(痛苦的 KYC 经历，对开始需要什么的困惑)
*   用户交互(地址、不熟悉的登录/模式/动作、终结效果、过于混乱的视图)
*   教育(wtf 是加密货币？区块链？钱包？交流？气体？)
*   价格稳定(我为什么要用这个？太不稳定了)
*   密钥管理(私钥的存储、使用和添加/删除)
*   成本(可扩展性，eth 2.0/“Caspar McShardface”可被视为 UX 的“终极”长期目标)

密钥管理也许是一个合理的起点(与可伸缩性和稳定性相比！)密钥管理有许多不同的风格，并且从早期的密码学开始就作为一个研究领域存在。的确，对于加密货币的密钥管理问题，可以提供许多链外解决方案，例如 [Shamir 的秘密共享](http://point-at-infinity.org/ssss/)、[分布式密钥](https://github.com/dfinity/dkg)、生成[存储](https://wiki.parity.io/Secret-Store)服务。然而，每一个都有自己的一系列挑战，UX 当然也包括在内。

一组更简单、更容易实现的解决方案可能属于链上密钥管理的范畴。许多用户熟悉私钥的概念，或者至少在简单解释后理解这个概念。真正的难点在于这些密钥的实际管理，用户通常没有兴趣承担安全存储、备份、恢复和更改这些密钥所需的工作。受 Multisig 启发的链上密钥管理为其中一些挑战提供了解决方案。

**多什么？**

多重签名钱包对于加密货币领域的许多人来说是一个熟悉的概念，但尽管其概念过于简单，许多不熟悉该领域的人并不理解这个术语的含义，也不理解功能性多重签名结构的价值。为清楚起见，在本文中，我们认为多签名合同(通常)是一种设置，在发送/花费交易之前需要 m/n 签名(授权)。这些结构存在于传统银行业务中——在一个常见的简单案例中，两个合作伙伴可能有一个联合银行账户，每个人都有自己的借记卡/信用卡，从这个联合账户中消费。在密码术语中，这被认为是 1/2 多重信号。以太坊灵活的启动契约语言允许我们构建更高级的多重签名契约，施加任意的限制和允许，以及可调整的许可。

一种有趣的密钥管理方法是使用与多重签名结构相同的概念，但应用于私钥本身——例如，可以授权添加另一个私钥，而不是 m-of-n 授权消费。这可以称为“链上密钥管理”。这个想法(加上更多)是通过 [ERC-725](https://github.com/ethereum/EIPs/issues/725) 身份标准提案表达的。这个提议涵盖的不仅仅是在一个链上管理密钥的能力；可以认为它更接近于一个[自我主权身份](http://www.lifewithalacrity.com/2016/04/the-path-to-self-soverereign-identity.html)的规范(这些概念都是紧密相连的)，但是关键的管理原则是本文的主要兴趣所在。该标准已经获得了业界的极大关注，一个专门鼓励该标准发展的联盟[成立了](http://erc725alliance.org/)。我们将从讨论该标准开始，查看该标准的一个示例实现，检查它如何影响 UX，并讨论该标准的扩展和限制。

**ERC-725**

从传统密钥管理的角度来看，ERC-725 是一个在线密钥管理系统。该标准草案中涉及的每个密钥或者是公钥，或者是对应的以太坊地址，对应于链外私钥。每个键都有相应的`keyType`、`purpose`和实际`key`值:

```
struct Key {
    uint256 purpose;
    uint256 keyType;
    bytes32 key;
}
```

到目前为止，已经定义了四个关键用途:`MANAGEMENT`、`ACTION`、`CLAIM`、&、`ENCRYPTION`，按数值升序排列。`MANAGEMENT`允许密钥执行管理类型的操作，例如添加或删除另一个密钥。`ACTION`键不太“强大”,可能仅限于发送非管理事务。`keyType`指定密钥相关的加密原语；RSA，椭圆曲线等。，尽管人们关心的是可以装入 32 个字节(256 位)的密钥类型的数量。在 ERC-725 中，没有与每个密钥相关联的截止日期，这与 uPort 的 ERC-1056 提议形成对比。

索赔(或证明)和这些索赔的管理( [ERC-735](https://github.com/ethereum/EIPs/issues/725) 索赔持有人)是 ERC-725 的一个很大的交织部分，但如前所述，主要焦点是检查关键的管理方面和这些带来的可用性的增加。ERC-725 标准规定了实现应该遵守的高级功能，这些功能被认为符合该标准。为了说明的目的，更容易集中在一个实现上；处于[地位的](https://status.im/)，这是一家业内知名公司，目标是成为以太坊生态系统的一种移动网关。在他们的 [github 库](https://github.com/status-im/contracts/tree/73-economic-abstraction/contracts/identity)中感兴趣的是 [ERC725](https://github.com/status-im/contracts/blob/73-economic-abstraction/contracts/identity/ERC725.sol) 和 [Identity](https://github.com/status-im/contracts/blob/73-economic-abstraction/contracts/identity/Identity.sol) Solidity 契约。这不是实现该标准的唯一方法，但是突出了与在 Solidity 中编写智能合同相关的一些特征、模式和挑战。

**mynameisidentity.sol**

Identity.sol 继承了 ERC725 和 ERC735(在这个分析中我们将忽略它们)，这两个抽象的“接口”样式的契约。这意味着 Identity.sol 必须定义以下函数:

```
function getKey(bytes32 _key, uint256 _purpose) public view returns(uint256 purpose, uint256 keyType, bytes32 key); function getKeyPurpose(bytes32 _key) public view returns(uint256[] purpose); function getKeysByPurpose(uint256 _purpose) public view returns(bytes32[] keys); function addKey(bytes32 _key, uint256 _purpose, uint256 _keyType) public returns (bool success); function removeKey(bytes32 _key, uint256 _purpose) public returns (bool success); function execute(address _to, uint256 _value, bytes _data) public returns (uint256 executionId); function approve(uint256 _id, bool _approve) public returns (bool success);
```

在讨论这些函数之前，我们定义了一些用于存储密钥的契约级变量、它们的相应用途以及每个密钥所需的或与之相关联的各种阈值:

```
mapping(bytes32 => Key) keys; //keccak256(key, purpose)=> Key Structmapping(uint256 => bytes32[]) keysByPurpose; //keys corresponding to each key purpose type (MANAGEMENT,ACTION, etc)mapping(bytes32 => uint256) indexes; //indices of active keysmapping(uint256 => uint256) purposeThreshold; //how many of keys are required to sign for that key purpose type (example: min of 1 for MANAGEMENT key)
```

此外，交易被定义为:

```
struct Transaction { 
   bool valid;  // flag to mark if tx is valid
   address to;
   uint256 value;
   bytes data;
   uint256 nonce; // tx nonce, not contract nonce
   uint256 approverCount; // incremented by approve function
   mapping(bytes32 => bool) approvals; //which keys have approved tx
}
```

事务存储在映射中`txx` —事务可能正在等待其他键的批准，这也防止了一种形式的重放攻击漏洞以及合同级别`nonce`。为了检查关键管理功能，本合同的恢复相关字段/功能(`recoveryContract`、`recoveryManager`)在本分析中也被忽略。

```
mapping (uint256 => Transaction) txx; // generally should correspond to nonce => Transactionuint256 nonce; // tx nonce to prevent replay attacks
```

**添加、移除和更换钥匙**

从外部可调用的“包装器”函数`addKey(bytes32 _key, uint256 _purpose, uint256 _keyType) public managementOnly returns (bool success)`开始，我们回忆起`_key`参数可以是通用公钥或以太坊地址。实际上，在前面讨论的 multisig 环境中，该功能相当于添加一个授权消费者。

```
function addKey(bytes32 _key, uint256 _purpose, uint256 _type)
public        
managementOnly        
returns (bool success)    
{
    _addKey(_key, _purpose, _type);
    return true;
}
```

`managementOnly`修饰符将对该函数的访问限制为内部调用，或者确保`msg.sender`是一个`MANAGEMENT`键。它通过无处不在的函数`isKeyPurpose(bytes32 _key, uint256 _purpose)`来实现。该功能检查`keys[keccak256(_key, _purpose)].purpose == _purpose`是否。注意，`keys`映射不存储`key.value => Key`的直接映射，而是存储键值和目的类型的 keccak 散列(`keyHash`)。`isKeyPurpose()`在整个合同中被调用，从而减少冗余代码，这是智能合同设计中的一个基本特征(由于部署/存储的高成本和安全分析/审计的增加的简易性)。

如果`managementOnly` 修饰符允许函数调用继续，则调用内部受限的`_addKey(bytes32 _key, uint256 _purpose, uint256 _type)`函数。该函数为传递的键计算`keyHash`，确保该键尚未被添加，确保`Key.keyType`已定义，然后将`Key`添加到由`keyHash`索引的`key`映射中。

```
function _addKey( bytes32 _key, uint256 _purpose, uint256 _type ) private 
{ 
    bytes32 keyHash = keccak256(_key, _purpose);
    require(keys[keyHash].purpose == 0); 

    require( _purpose == MANAGEMENT_KEY || _purpose == ACTION_KEY ||
    _purpose == CLAIM_SIGNER_KEY || _purpose == ENCRYPTION_KEY );

    keys[keyHash] = Key(_purpose, _type, _key);
    indexes[keyHash] = keysByPurpose[_purpose].push(_key) — 1;
    emit KeyAdded(_key, _purpose, _type);
}
```

使用`_purpose`作为索引，`_key`也被推入到`keysByPurpose`映射中。例如，如果添加的是一个`ACTION`键，这将增加存储在`ACTION`索引下的键的数量(在`ACTION`的情况下为 2)。

还发出`KeyAdded` [事件](https://rinkeby.etherscan.io/tx/0x1071719b33598d3dbc8650cdba19700e268b025f293df7ccd6feb01d99f2ee64#eventlog)，以便以太坊区块链之外的任何钱包/基础设施都可以反应/触发。

`[removeKey(bytes32 _key, uint256 _purpose) public returns (bool success)](https://github.com/status-im/contracts/blob/73-economic-abstraction/contracts/identity/Identity.sol#L147)`功能的工作方式与`addKey()`基本相同，但(如预期的)方向相反。使用相同的`managementOnly`限制器。键从`key`映射中删除，并从`keysByPurpose`映射中移除。一个`KeyRemoved`事件也被触发。

在 ERC-725 标准上以`replaceKey`函数的形式提供了额外的功能，它先调用`_addKey()`，然后调用`_removeKey()`。

**ERC-725 所需的其他关键功能**

`getKeyPurpose(bytes32 _key)`和`getKeysByPurpose(uint256 _purpose)`的行为与预期完全一致，其中`getKeyPurpose`返回为被查询的`_key`存储的目的。返回合同与那个`_purpose`关联的所有键。

**批准并执行**

Approve 和 execute 是导致事务执行的两个外部可调用函数，前提是从适当的键类型获得足够的批准。这些函数是发送交易的主要接口。

```
function execute(address _to, uint256 _value, bytes _data)         public 
returns (uint256 executionId)    
{        
    uint256 requiredKey = _to == address(this) ? MANAGEMENT_KEY :
    ACTION_KEY;
    if (purposeThreshold[requiredKey] == 1) {
        executionId = nonce;          
        nonce++;            
        require(isKeyPurpose(bytes32(msg.sender), requiredKey));
        _to.call.value(_value)(_data); 
        emit Executed(executionId, _to, _value, _data);
    } 
    else {
        executionId = _execute(_to, _value, _data); 
        approve(executionId, true);
    }
}
```

`execute()`检查交易目的地是否是合同本身，如果是，则需要`MANAGEMENT`键，如果不是`ACTION`键。检查密钥类型的`purposeThreshold`—如果该密钥用途类型只需要一个密钥，则使用`isKeyPurpose`检查该密钥是否具有该用途，并使用低级调用`_to.call.value(_value)(_data)`发送交易。如果特定键用途类型的`purposeThreshold`不等于 1，则调用内部`_execute()`。这个内部函数构造一个`Transaction`，设置`approvalCount=0`，将其插入到`txx`映射中，发出一个`ExecutionRequested`事件并返回`executionID`，这是契约级别`nonce`。

然后使用`executionID`作为参数调用`approve()` “包装器”函数。满足`managerOrActor`修饰符后，调用`_approve(bytes32(msg.sender), executionID, _approval=true)`。

```
function _approve(bytes32 _key, uint256 _id, bool _approval)         private         
returns(bool success)
{                
    Transaction memory trx = txx[_id];
    require(trx.valid);        
    uint256 requiredKeyPurpose = trx.to == address(this) ?
    MANAGEMENT_KEY : ACTION_KEY;

    require(isKeyPurpose(_key, requiredKeyPurpose));
    bytes32 keyHash = keccak256(_key, requiredKeyPurpose);
    require(txx[_id].approvals[keyHash] != _approval);

    if (_approval) {
        trx.approverCount++;
    }
    else {
        trx.approverCount--;
    }
    emit Approved(_id, _approval);

    if (trx.approverCount<purposeThreshold[requiredKeyPurpose]) {
        txx[_id].approvals[keyHash] = _approval;
        txx[_id] = trx;
    } 
    else {
        delete txx[_id];
        success = address(trx.to).call.value(trx.value)(trx.data);            
        emit Executed(_id, trx.to, trx.value, trx.data);
    }
}
```

从`txx`数组加载事务并检查其有效性。有趣的是，这里进行了优化以将事务加载到内存中(在基于 256 位堆栈的 EVM 中有三个内存“区域”——堆栈、内存和存储，按运行成本升序排列，按持久性和可访问性降序排列)。检查`isKeyPurpose`并找到传递的键的`keyHash`。事务处理的批准数组被检查为假。交易的`approverCount`字段递增，并且如果对于`requiredKeyPurpose`有足够的`purposeThreshold`批准，则交易被发送:`address(trx.to).call.value(trx.value)(trx.data).`如果没有足够的来自所需关键用途类型的适当批准，则该批准被添加到交易`approvals`字段并保存回存储器，等待进一步所需的批准。

这个实现展示了链上密钥管理的灵活性和多功能性，但也展示了智能合约变得复杂的速度。这种实现忽略了拥有不同`keyType` (RSA、EC 等)的能力。)因为这肯定会增加合同的复杂性。

**一次性钥匙和无汽油交易**

设备丢失(不幸的是)很常见。最近的 EOS ICO token 到 EOS mainnet 的移植/转移事件表明，大约 5%的用户丢失了他们的私钥，无法在时机成熟时将资金转移到 EOS mainnet。这些用户中的大多数可能是相对精通技术的，他们首先参与了 ICO，随着采用率的增加和技术意识较弱的用户加入进来，这一数字有望增加。

*一次性键:*这些键更准确的术语是“上下文特定的应用程序键”。良好的用户体验包括由于丢失包含密钥的设备而遭受资金的完全损失。理想情况下，一次性密钥不包含货币值，但可能包含许可值。虽然非加密用户可能不太熟悉密钥管理的过程，但他们可能熟悉注销丢失的银行卡的过程，这是这种模式所模仿的。

ERC-725 促进了一次性密钥，天真地作为一个`ACTION`密钥，结合由 [IdentityGasRelay](https://github.com/status-im/contracts/blob/73-economic-abstraction/contracts/identity/IdentityGasRelay.sol) 中的逻辑引入的元事务能力。sol，它继承 Identity.sol 协定。各种关键授权用于支持无汽油交易功能。这份合同将在另一篇博文中分析。

*元或“无气”交易*:这与用户教育和密钥管理都有关系。不应期望最终用户理解停机问题或气体在防止停机中的作用。“其他人”需要为从一个设备发起的交易支付汽油，如果该设备包含一次性密钥并且该密钥不包含乙醚，因此没有能力发送交易。其他人可能是一群受到经济激励的中继者，或者仅仅是一个基于云的服务器。

有[许多](/@austin_48503/ethereum-meta-transactions-90ccf0859e84)不同的[方法](/gitcoin/technical-deep-dive-architecture-choices-for-subscriptions-on-the-blockchain-erc948-5fae89cabc7a)来实现这一点，有几个不同的项目从不同的角度来应对挑战。事实上，一些链上架构模式对于订阅服务是有用的。 [ERC-1077](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1077.md) 是一个标准化这些可执行签名消息方法的提案，而 [ERC-948](https://github.com/ethereum/EIPs/issues/948) 试图对基于订阅的支付做同样的事情。

**限制**

**隐私**:和大多数以太坊基于链的计算一样，隐私是有限的。不考虑 ERC-735 公开证明/声明的隐私含义，在 ERC-725 中存在隐私泄露。例如，我想让整个区块链观测世界知道我添加了一个新的`MANAGEMENT`键吗？这很容易辨认。这意味着什么？这是否会转化为现实世界风险的增加，以及如何转化？

最终，通过使用零知识证明可以获得许多隐私方面的好处，这似乎特别适用于 ERC-735 声明，而不是该标准的关键管理方面。各种风格的证明将被构建，但是以太坊的当前状态验证起来很昂贵，而且实际上不可能生成，尽管存在验证预编译。

**成本**:链上交易(目前)很贵。链上[定制加密操作](https://eprint.iacr.org/2017/110.pdf)甚至更昂贵。UX 的最终目标是将可伸缩性提高到链上计算非常便宜的程度，以至于这类交易的成本实际上可以忽略不计，比如低于 0.05 美元，这是对当前发行新借记卡/信用卡的传统银行收费的重大改进。目前，部署的示例合同成本为 4.17 美元(@ 1ETH = 206 美元，平均交易接受时间气价为 5GWei)。添加第一个密钥的成本约为 0.14 美元。而移除第一个密钥的成本约为 0.04 美元。由于索引和存储相关的成本，这些成本会随着添加的密钥数量而略有不同。虽然部署可能被认为是昂贵的，但是添加和移除密钥是成本有效的。

解释 Identity.sol 和 [IdentityGasRelay](https://github.com/status-im/contracts/blob/73-economic-abstraction/contracts/identity/IdentityGasRelay.sol) 的作用的后续帖子。无气交易的 sol 即将到来。

在媒体上关注我或在推特上联系我:【https://twitter.com/gawnieg 

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)