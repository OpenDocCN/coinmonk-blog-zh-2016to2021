# 通过测井将数据存储在以太坊中以减少气体成本

> 原文：<https://medium.com/coinmonks/store-data-in-ethereum-by-logging-to-reduce-gas-cost-b70a13884485?source=collection_archive---------0----------------------->

## 我提出了一个现实的解决方案，使用以太坊作为公共账本

![](img/01e9f8bce4bdc52b08e4fdd48602b149.png)

# 问题是天然气成本和容量

使用以太坊作为公共账本，我们将面临两个问题。第一个是燃气成本。将数据存储到存储器是最昂贵的操作。(见[s 储气成本](https://github.com/crytic/evm-opcodes))另一个是产能。事件可靠性似乎[没有任何数据大小限制](https://www.reddit.com/r/ethereum/comments/3kgxlx/in_solidity_does_bytes_have_a_maximum_length/)，我们受到气体限制。

因此，目前的以太坊不适合大数据或频繁存储。

# 以太坊是参照物，IPFS 是存储

在分布式存储上存储数据，比如 IPFS。IPFS 没有任何数据大小限制。然后，为了数据完整性，将散列存储到以太坊。IPFS 哈希真的很小，所以这有助于节省天然气成本。这是最有可能采取的解决方案。以太网的著名项目，像 [Origin Protocol](https://github.com/OriginProtocol/origin/blob/master/packages/contracts/contracts/marketplace/V01_Marketplace.sol) 或者 [Openlaw](https://media.consensys.net/introducing-openlaw-7a2ea410138b) 都在做。

为了进一步降低天然气成本，我们建议按事件排放存储数据。除非您从智能合约中删除数据或操作数据，否则记录就足够了。

# 你是如何实现的？

我们定义下列事件。

```
event Recorded (bytes4 indexed eventSig, uint256 indexed createdAt, bytes32 ipfsHash);
```

“eventSig”类似于事件名称。指定任意长度的字符串都没有关系。虽然最好指定固定长度的短字母以节省汽油费用。我们将“evetSig”定义为事件名称的 Keccak256 哈希的前 4 个字节。这个规则类似于'[功能选择器](https://solidity.readthedocs.io/en/v0.5.12/abi-spec.html#function-selector)

createdAt 是自我解释的。发出此事件的数据时间。

放置“ipfsHash”来指定 IPFS 哈希。请注意参数类型不是“字符串”。参见下面的例子，IPFS 散列的长度固定为 46。

```
QmXbTtSAPJ545YRnLt7n7ngMa4ZTmizmznshZZjXDRhYih
```

[根据 solidity specific，](https://solidity.readthedocs.io/en/v0.5.12/abi-spec.html#types)字符串数据编码为 utf8，因此 IPFS 哈希为 46 字节大小。

[根据 solidity specific](https://solidity.readthedocs.io/en/v0.5.12/abi-spec.html#formal-specification-of-the-encoding) ，该字符串数据消耗 64 个字节的存储空间，因为 18 个字节用尾随零字节填充到 32 个字节的长度。然后我们用 base58 解码 IPFS 哈希。([最初，IPFS 散列串是 base58 编码的](https://ethereum.stackexchange.com/questions/44506/ipfs-hash-algorithm))base58 解码的 IPFS 散列是 32 字节大小，因此我们实现了减少 32 字节。这有助于节省燃气成本。

# 示例实现

这是实现示例。

不应存储不必要的日志，以便只有被允许的人才能发出“记录的”事件。因此，我们设置了“onlyWhitelistAdmin”修饰符。

```
import 'openzeppelin-solidity/contracts/access/roles/WhitelistAdminRole.sol';

event Recorded (bytes4 indexed eventSig, uint256 indexed createdAt, bytes32 ipfsHash);

/**
 * @dev Write ipfsHash as log
 */
function writeHash(bytes4 _eventSig, bytes32 _ipfsHash) public onlyWhitelistAdmin {
    emit Recorded(_eventSig, uint256(now), _ipfsHash);
}
```

# 用例

我们可以存储各种数据。

## 案例 1。证书

存储在以太坊上的认证哈希永远不会被更改，因此我们可以验证认证的完整性。只要认证发布者运行 IPFS 节点，它就可以随时公开使用。

## 案例 2。版权

同样，我们可以公开验证版权的完整性。

## 案例 3。代币

我们可以在此基础上发明类似 ERC20 的令牌协议。例如，我们将“eventSig”定义为标记名和函数的连接

```
eventSig => First 4 bytes of Keccak256(“token_name:transferFrom”)
```

然后，我们把其他信息(从，到，价值)给 IPFS

```
{
 from: 0x825bBB7fdD9292ba18913c1E784D839DbEF82BbB,
 to: 0x49Cb60853D1a527995F6CB784e9641bb408dBb51,
 value: 10000000
}
```

## 讨论

请在这里告诉我你的看法， [ERC 2307](https://github.com/ethereum/EIPs/issues/2307) 。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)