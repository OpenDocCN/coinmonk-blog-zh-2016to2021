# 股权化:介绍 ERC-884

> 原文：<https://medium.com/coinmonks/tokenising-shares-introducing-erc-884-cc491258e413?source=collection_archive---------3----------------------->

![](img/0ad028f7646a55b78434d7e9d0f8cd9d.png)

Digital sunset on an analogue world.

特拉华州立法机关最近通过了[特拉华州参议院，第 149 届大会，参议院法案第 69 号:一项修正与普通公司法](https://legis.delaware.gov/json/BillDetail/GenerateHtmlDocument?legislationId=25730&legislationTypeId=1&docTypeId=2&legislationName=SB69)相关的《特拉华州法典》第 8 篇的法案，以下简称为“法案”。因此，特拉华州普通公司法现在明确允许使用区块链来维护公司股份登记册。

考虑到这一点，我很兴奋地宣布`[ERC-884](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-884.md)`已经被接受为以太坊令牌规范草案。`ERC-884`允许创建可交易的`ERC-20`代币，每个代币代表一家特拉华州公司发行的无数(或普通)股票。`ERC-884`旨在代表任何特拉华州公司发行的股权，无论是私有还是公有。

通过部署`ERC-884`令牌，公司可以通过首次公开募股(IPO)或私募股权出售筹集资金，以符合特拉华州公司法的方式，但绕过了定制股票注册的需要，或传统证券交易所或转让代理的参与。

## 核心原则

`ERC-884`除了基本的`ERC-20`标准之外，令牌还支持符合以下原则:

令牌所有者必须验证其身份。`ERC-884`要求所有令牌所有者都被列入白名单，而不是留给单独的众筹合同来强制实施所有者白名单。白名单可以被附带的众筹合同引用，以加强资格，但白名单仍然是令牌合同本身不可分割的一部分。

代币合同必须提供 a `Corporations Stock Ledger`的以下三个功能(参考:法案第 224 节):

1.  它必须使公司能够编制《公司法》第 219 条和第 220 条规定的股东名单。
2.  它必须记录该法第 156、159、217(a)和 218 节规定的信息。
3.  它必须记录标题 6 副标题 I 第 8 条规定的股份转让。

## 没有 P*artial*令牌

每个`ERC-884`代币*必须*对应一股*未编号的*股票，每股必须全额支付。没有必要记录关于部分支付股份的信息。类似地，不要求`ERC-884`令牌对应特定编号的份额。知道一个股东拥有一定量的股份就足够了。在这种情况下，单独编号股票的概念是不必要的负担。

最后，必须有一种机制来允许丢失了他们的私钥或者以其他方式丢失了对他们的代币的访问权的股东取消他们的地址，并且将他们的代币重新发放到新的地址。

## 证券交易委员会要求

证券交易委员会(SEC)对众筹销售应该如何运作以及必须向公众提供哪些信息有额外的要求。然而，这个信息超出了`ERC-884`标准的范围，尽管该标准支持这样的需求。

例如:美国证券交易委员会(SEC)要求众筹网站显示以美元计的筹资额。为了支持这一点，铸造这些代币的众筹销售合同必须保持美元对 ETH 的兑换率(通过 oracle 或一些其他机制),并且必须记录铸造时使用的兑换率。

此外，根据融资的类型，SEC(或其他法定机构)可以对允许的股东数量进行限制。为了支持这一点，该标准提供了`holderCount`和`isHolder`功能，众卖可以调用这些功能来检查是否超出了限制。

## 身份 H 的使用`ash`

为了遵守该法案，众筹销售的实施者必须能够提供所有股东的最新姓名和地址列表。出于隐私和经济的原因，不希望在公开的区块链中包括这些细节。强烈建议不要在区块链上存储任意字符串数据。

实施者必须维护一个*链外*私有数据库，该数据库记录所有者的姓名、物理地址以及用于拥有其`ERC-884`令牌的以太坊地址。然后，实现者必须能够提取任何以太坊地址的名称和物理地址，散列该数据，并使用`hasHash`函数将结果散列与合同中记录的散列进行比较，从而确认他们信息的真实性。该系统的具体细节留给实施者。

还希望实现者提供一个 REST API 端点，大致如下

```
GET https://<host>/<pathPrefix>/:ethereumAddress -> [true|false]
```

以使第三方审计员能够验证给定的以太坊地址对于实施者来说是已知的已验证地址。

具体实现者如何验证一方的身份由他们自己决定，这超出了本标准的范围。

## 无法访问其共享的用户

传统的股份登记通常由转让代理人管理，该代理人被授权准确维护登记册，并处理股东查询。一个常见的要求是在股东丢失或销毁其原始凭证的情况下重新发行股票。

令牌实现者可以通过`cancelAndReissue`函数处理这个请求，该函数必须执行各种更改，以确保旧地址现在指向新地址，并且被取消的地址不会被重用。

# 技术细节:延长 ERC-20

`ERC20`令牌提供了以下基本特性，这里用 Solidity 代码表示:

```
contract ERC20 {
  function totalSupply() public view returns (uint256); function balanceOf(address who) public view returns (uint256); function transfer(
      address to,
      uint256 value
  ) public returns (bool); function allowance(
      address owner,
      address spender
  ) public view returns (uint256); function transferFrom(
      address from,
      address to,
      uint256 value
  ) public returns (bool); function approve(
      address spender,
      uint256 value
  ) public returns (bool); event Approval(
      address indexed owner,
      address indexed spender,
      uint256 value
  ); event Transfer(
      address indexed from,
      address indexed to,
      uint256 value
  );
}
```

这由`ERC-884`扩展如下:

```
/**
 *  An `ERC20` compatible Token that conforms to
 *  Delaware State Senate, 149th General Assembly,
 *  Senate Bill No. 69: An act to Amend Title 8
 *  of the Delaware Code Relating to the General Corporation Law.
 *
 *  Implementation Details.
 *
 *  An implementation of this Token standard SHOULD
 *  provide the following:
 *
 *  `name` - for use by wallets and exchanges.
 *  `symbol` - for use by wallets and exchanges.
 *
 *  The implementation MUST take care not to allow
 *  unauthorised access to stock transfer functions.
 *
 *  In addition to the above, the following optional
 *  `ERC20` function MUST be defined.
 *
 *  `decimals` — MUST return `0` as each Token represents
 *  a single Share and Shares are non-divisible.
 *
 */
contract ERC884 is ERC20 { /**
     *  This event is emitted when a verified address
     *  and associated identity hash are added to the contract.
     *
     *  @param addr The address that was added.
     *  @param hash The identity hash associated with the address.
     *  @param sender The address that caused the address
     *                to be added.
     */
    event VerifiedAddressAdded(
        address indexed addr,
        bytes32 hash,
        address indexed sender
    ); /**
     *  This event is emitted when a verified address and its
     *  associated identity hash are removed from the contract.
     *
     *  @param addr The address that was removed.
     *  @param sender The address that caused the address
     *                to be removed.
     */
    event VerifiedAddressRemoved(
        address indexed addr,
        address indexed sender
    ); /**
     *  This event is emitted when the identity hash associated
     *  with a verified address is updated.
     *
     *  @param addr The address whose hash was updated.
     *  @param oldHash The identity hash that was associated
     *                 with the address.
     *  @param hash The hash now associated with the address.
     *  @param sender The address that caused the hash to
     *                be updated.
     */
    event VerifiedAddressUpdated(
        address indexed addr,
        bytes32 oldHash,
        bytes32 hash,
        address indexed sender
    ); /**
     *  This event is emitted when an address is cancelled and
     *  replaced with a new address.  This happens in the case
     *  where a shareholder has lost access to their original
     *  address and needs to have their share reissued to a new
     *  address.  This is the equivalent of issuing replacement
     *  share certificates.
     *
     *  @param original The address being superseded.
     *  @param replacement The new address.
     *  @param sender The address that caused the address
     *                to be superseded.
     */
    event VerifiedAddressSuperseded(
        address indexed original,
        address indexed replacement,
        address indexed sender
    ); /**
     *  Add a verified address, along with an associated
     *  verification hash to the contract. Upon successful
     *  addition of a verified address, the contract must emit
     *  `VerifiedAddressAdded(addr, hash, msg.sender)`.
     *  It MUST throw if the supplied address or hash are zero,
     *  or if the address has already been supplied.
     *
     *  @param addr The address of the person represented
     *              by the supplied hash.
     *  @param hash A cryptographic hash of the address-holder's
     *              verified information.
     */
    function addVerified(address addr, bytes32 hash) public; /**
     *  Remove a verified address and the associated
     *  verification hash.
     *  If the address is unknown to the contract then this 
     *  does nothing.
     *  If the address is successfully removed, this function
     *  must emit `VerifiedAddressRemoved(addr, msg.sender)`.
     *  It MUST throw if an attempt is made to remove a 
     *  verifiedAddress that owns Tokens.
     *
     *  @param addr The verified address to be removed.
     */
    function removeVerified(address addr) public; /**
     *  Update the hash for a verified address
     *  known to the contract.
     *  Upon successful update of a verified address
     *  the contract must emit
     *  `VerifiedAddressUpdated(addr, oldHash, hash, msg.sender)`.
     *  If the hash is the same as the value already stored then
     *  no `VerifiedAddressUpdated` event is to be emitted.
     *  It MUST throw if the hash is zero, or if the
     *  address is unverified.
     *
     *  @param addr The verified address of the person
     *              represented by the supplied hash.
     *  @param hash A new cryptographic hash of the address-holder's 
     *              updated verified information.
     */
    function updateVerified(address addr, bytes32 hash) public; /**
     *  Cancel the original address and reissue the
     *  Tokens to the replacement address.
     *  Access to this function MUST be strictly controlled.
     *  The `original` address MUST be removed from the
     *  set of verified addresses.
     *  Throw if the `original` address supplied
     *  is not a shareholder.
     *  Throw if the `replacement` address is not
     *  a verified address.
     *  Throw if the `replacement` address already holds Tokens.
     *  This function MUST emit `VerifiedAddressSuperseded`.
     *
     *  @param original The address to be superseded.
     *                  This address MUST NOT be reused.
     */
    function cancelAndReissue(
        address original,
        address replacement
    ) public; /**
     *  The `transfer` function MUST NOT allow transfers
     *  to addresses that have not been verified and
     *  added to the contract.
     *  If the `to` address is not currently a shareholder
     *  then it MUST become one.
     *  If the transfer will reduce `msg.sender`'s balance
     *  to 0 then that address MUST be removed from the
     *  list of shareholders.
     */
    function transfer(
        address to,
        uint256 value
    ) public returns (bool); /**
     *  The `transferFrom` function MUST NOT allow transfers
     *  to addresses that have not been verified and added
     *  to the contract.
     *  If the `to` address is not currently a shareholder
     *  then it MUST become one.
     *  If the transfer will reduce `from`'s balance to 0 then
     *  that address MUST be removed from the list of shareholders.
     */
    function transferFrom(
        address from,
        address to,
        uint256 value
     ) public returns (bool); /**
     *  Tests that the supplied address is known to the contract.
     *
     *  @param addr The address to test.
     *  @return true if the address is known to the contract.
     */
    function isVerified(address addr) public view returns (bool); /**
     *  Checks to see if the supplied address is a shareholder.
     *
     *  @param addr The address to check.
     *  @return true if the supplied address owns a token.
     */
    function isHolder(address addr) public view returns (bool); /**
     *  Checks that the supplied hash is associated with
     *  the given address.
     *
     *  @param addr The address to test.
     *  @param hash The hash to test.
     *  @return true if the hash matches the one supplied
     *               with the address in `addVerified`, or
     *               `updateVerified`.
     */
    function hasHash(
         address addr,
         bytes32 hash
    ) public view returns (bool); /**
     *  The number of addresses that hold tokens.
     *
     *  @return the number of unique addresses that hold tokens.
     */
    function holderCount() public view returns (uint); /**
     *  By counting the number of Token holders using `holderCount`
     *  you can retrieve the complete list of Token holders,
     *  one at a time.
     *  It MUST throw if `index >= holderCount()`.
     *
     *  @param index The zero-based index of the holder.
     *  @return the address of the Token holder with
     *          the given index.
     */
    function holderAt(uint256 index) public view returns (address); /**
     *  Checks to see if the supplied address was superseded.
     *
     *  @param addr The address to check.
     *  @return true if the supplied address was
     *               superseded by another address.
     */
    function isSuperseded(address addr) public view returns (bool); /**
     *  Gets the most recent address, given a superseded one.
     *  Addresses may be superseded multiple times, so this
     *  function needs to follow the chain of addresses until it 
     *  reaches the final, verified address.
     *
     *  @param addr The superseded address.
     *  @return the verified address that ultimately
     *          holds the stock.
     */
    function getCurrentFor(
        address addr
    ) public view returns (address);
}
```

## 测试案例和参考实现

测试案例和参考实施可在[github.com/davesag/ERC884-reference-implementation](https://github.com/davesag/ERC884-reference-implementation)获得。

## 权限管理

不希望任何人可以添加、删除、更新或替换经过验证的地址。如何控制对这些功能的访问超出了本标准的范围，但是参考实现提供了一种简单的访问控制形式，可以很容易地进行扩展。

## 向后兼容性

`ERC-884`标准在现有`ERC-20`标准的基础上提供了尽可能小的扩展，以符合法案的要求，同时保持与`ERC-20`本身的兼容性。

该标准旨在保持与`ERC-20`令牌的兼容性，但有以下附带条件:

1.  `decimals`函数必须返回`0`，因为令牌不能被分割。
2.  `transfer`和`transferFrom`功能不得允许转移到未经验证的地址，并且必须维护股东列表。
3.  转让剩余股票的股东必须从股东名单中除名。

但书 1 并没有破坏与现代钱包或交易所的兼容性，因为它们似乎都在使用可用的信息。

如果试图将`transfer`令牌发送到未经验证的地址，但书 2 将导致`transfer`失败。这是隐含在设计中的，我们鼓励实施者向市场参与者充分说明这一点。人们普遍认为，这将使某些交易所对 `ERC-884`感到不快，但美国证券交易委员会要求公司股东提供经核实的姓名和地址。

但书 3 是实现细节。

此外，`ERC-884`要求实现`throw`出错(最好通过使用[即将出现的](https://github.com/ethereum/solidity/issues/1686#issuecomment-328181514) `[require(condition, 'fail message')](https://github.com/ethereum/solidity/issues/1686#issuecomment-328181514)` [语法](https://github.com/ethereum/solidity/issues/1686#issuecomment-328181514))，而不是为成功或不成功完成诸如`addVerified`、`removeVerified`和`updateVerified`之类的状态改变函数返回`bool`。

## 结论

`ERC-884`代币提供了一种机制，通过这种机制，特拉华州公司可以使用以太坊智能合约(托管在以太坊区块链上，而不是使用传统机制)来维护官方股份登记册。

通过保持与`ERC-20`代币的兼容性，`ERC-884`代币可以通过任何支持`ERC-20`代币交易的加密货币交易所进行交易，从而允许公司避开使用传统的证券交易所、股票登记处或转让代理。

参考`ERC-884`实现已经在[麻省理工学院开源许可](https://opensource.org/licenses/MIT)下发布。我们鼓励感兴趣的开发人员在他们认为合适的时候进行试验。

## 谢谢

我要感谢 [C4Coin](https://www.c4coin.org) 的 Harrison Perl 和 Tigran Avakyan 为`ERC-884`的创作提供的宝贵意见，以及 Ross Williams 博士和 [Industrie & Co](https://industrie.co) 的 Adrian Andreacchio 对本文的详细校对。

## 链接

*   原`ERC-20`提案:[github.com/ethereum/EIPS/eip-20.md](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md)
*   github.com/ethereum/EIPS/eip-884.md 的`ERC-884`提案:
*   `ERC-884`接口定义和引用实现:[github.com/davesag/ERC884-reference-implementation](https://github.com/davesag/ERC884-reference-implementation)

—

像这样但不是订户？你可以通过[davesag.medium.com](https://davesag.medium.com/membership)加入来支持作者。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [币安交易机器人](/coinmonks/binance-trading-bots-d0d57bb62c4c) | [OKEx 审查](/coinmonks/okex-review-6b369304110f) | [Atani 审查](https://coincodecap.com/atani-review)
*   [最佳加密交易信号电报](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) | [MoonXBT 评论](/coinmonks/moonxbt-review-6e4ab26d037)
*   [如何在 Bitbns 上购买柴犬(SHIB)币？](https://coincodecap.com/buy-shiba-bitbns) | [买弗洛基](https://coincodecap.com/buy-floki-inu-token)
*   [CoinFLEX 评论](https://coincodecap.com/coinflex-review) | [AEX 交易所评论](https://coincodecap.com/aex-exchange-review) | [UPbit 评论](https://coincodecap.com/upbit-review)
*   [十大最佳加密货币博客](https://coincodecap.com/best-cryptocurrency-blogs) | [YouHodler 评论](https://coincodecap.com/youhodler-review)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [杠杆代币](/coinmonks/leveraged-token-3f5257808b22)终极指南