# 了解握手 Airdrop 和保留名称

> 原文：<https://medium.com/coinmonks/understanding-the-handshake-airdrop-and-reserved-names-428d9e90b560?source=collection_archive---------0----------------------->

![](img/d3a38306465a76646c3aea92e17601de.png)

深入探究免费赠送的加密过程。

# 金钱和名字

握手区块链中储存着两种资产:钱和名字。

金钱在握手中的编码方式与比特币相同:有一组有效的交易输出(UTXOs 或“硬币”)，每个输出都有自己的值和锁定脚本。解锁一枚硬币，你可以用等值或等值以下的新硬币替换它。

名称在握手中以同样的方式编码。一旦一个名字在[拍卖](https://handshake-org.github.io/guides/auctions.html)中胜出，它就被一个 UTXO“拥有”。那个 UTXO 有一个永远不会被花掉的值(它被永久锁定，或者“烧毁”)，并且它有一个锁定脚本。解锁该脚本，您可以将 DNS 记录添加到名称中，甚至可以使用新的锁定脚本将名称转移到新的 UTXO 中。

# 免费的东西

握手项目的一个基本原则是让开源软件社区和互联网的整体基础设施受益。来自[的白皮书](https://handshake.org/files/handshake.txt):

> 正如资本主义在参与者之间创造了一个竞争性的游戏，在这个游戏中竞争的自身利益降低了非垄断的商品环境中的商品价格，握手机制是一个探索类似的并行游戏的项目，以最大化自由/开源软件开发者和公众的所有权。
> 
> 在资本主义市场上，没有哪一个生产者会为了利他主义而降低自己产品的价格，这是通过自利的竞争激励来实现的，“我降低价格，就能赚更多的钱”。类似地,
> 握手机制正在试验一个过程,“我赚的钱越多，自由/开源软件开发者和全人类得到的礼物就越多”。

为此， [70%的硬币供应](https://handshake.org/how-it-works)将分配给开源开发者和非营利组织。Alexa 排名前 10 万的网站中列出的 DNS 名称将被保留给它们当前的所有者，他们可以使用他们的握手钱包在链上声明这些名称。因此，当这个连锁店开张时，许多社区成员将会有免费的钱，许多主要的互联网资源提供商将会有他们自己的名字。这项计划也将防止像“脸书”这样的名字被恶意使用或抢注。

# 收集钥匙

过去的加密货币空投通常涉及拍摄当前网络 UTXO 集的快照，并从那里生成新的(不兼容的)区块链。无论谁持有原始链上的硬币，都可以使用相同的钥匙来解锁新链上的那些硬币的副本。握手项目不资助其他加密货币利益相关者，但是，它资助开源开发者——那么我们如何让他们得到他们的硬币？我们不知道他们的公钥…或者我们知道吗？

```
$ curl [https://api.github.com/users/pinheadmz/gpg_keys](https://api.github.com/users/pinheadmz/gpg_keys)
[
  {
    ...
    "public_key": "xsFNBFtrHssBEADlPQQ+eaXmIg/lDxCUuwJDnF0BXVoTKeqDIrDLx/2qFA6Dj6AcUWsrVNPxvvSMr2i5LgMFrFvjbrXoo7jsZO5a4AUuXragJzI6UKaKD5HxQR/6L6zXsOmGHhLMCAUwaIyl7wqOUrDaJbAbmr/S38yEhEkztVuxN8YbrZ6WpgEVMPfZsNdyUPEOEHUcRSwJGVGnn2CEPdUavFYIGYgGFxJBhy/xcRtgDrKyMhXa8qdj5IPksfGiSGC69PkbG2vT6Dy+Lmc2QhSv3cRn+rYnY8jdN9ADKY7um4XKwvlzkVld6MGDYJG/EB53Q+rAu+qbUhPrGOKRdC7hnangsALi6dwEq1XDk1n/Snp4/WR3oADFxo5WAGls+i+vxtQul3U+h67xyPRBaW/FZjgN8gNL97nIqMPa7HHDxBsobmrVYnbNnhiA84qXHDc/vgcSFlMjJ9il05m6JIvwpMCN+QaR2s5G0MW6lhY6qmBezBMwyy7eSMvRiAGpUg/KDTzqKj+x87VkYPb6YMoa/FHkV+dne/1Alb1Kp9+tvGe/FSjPBOPbXK+F1uRMC46O2lOpsAPo0DntI85c7/RlXUBlXk8En+FYies/a+vBNgEtZsmBVxGD77i+ThM7MOk+oGY8yON25OQOYXIoBT8FLGzUofM/LF5yERtcIf5lm48XNBOWhQr+xQARAQAB"
  }
]
```

GitHub API 为上传它们的用户返回公共 PGP 和 SSH 密钥。然而，GitHub 的 API 文档明确指出:

> 在`public_key`响应字段中返回的数据不是 GPG 格式的关键字。当用户上传 GPG 密钥时，会对其进行解析，并提取和存储加密公钥。这个密钥是由这个页面上的 API 返回的。这个键不适合被像 GPG 这样的程序直接使用。

但我会告诉你一个小秘密。OpenPGP 报文和包格式在 [RFC4880](https://tools.ietf.org/html/rfc4880) 中有规定，可以被 [bcrypto](https://github.com/bcoin-org/bcrypto) 解码:

```
$ node> const {PGPMessage} = require('bcrypto/lib/pgp')
> PGPMessage.fromBase64('xsFNBFtrHssBEADlPQQ+eaXmIg/lDxCUuwJD...'){ packets: [ { type: 'PUBLIC_KEY', body: [Object] } ] }
```

通过这种方式，从流行的开源贡献者那里为空投收集了数万个公钥。

还有一个水龙头网站，任何人都可以通过提供由[水龙头工具生成的 mainnet 握手地址来提交空投硬币的申请。](https://github.com/handshake-org/faucet-tool)电子邮件地址是必需的，已经收到 GitHub 按键空投的用户被从水龙头中移除。

# 摇钱树

对于这个演示，我们将使用[修改](https://github.com/pinheadmz/hs-airdrop/commits/guide) `[hs-airdrop](https://github.com/pinheadmz/hs-airdrop/commits/guide)` [存储库的一个分支。](https://github.com/pinheadmz/hs-airdrop/commits/guide)该存储库包含接收者可以用来在链上收集他们的硬币的脚本，但是它还包含握手开发者用来在 mainnet 启动之前将他们收集的所有公钥插入到区块链共识机制中的脚本。

长话短说，所有的接收者公钥都被加载到一个 Merkle 树中，这个树有一个一致性关键根[存储在 hsd 客户端](https://github.com/handshake-org/hsd/blob/cc35dc2a351eecd6adfae94b981a2d85277ecbb5/lib/primitives/airdropproof.js#L22)中。兑换空投硬币需要证明一个公钥存在于 Merkle 树中，然后用它签署一个特殊类型的交易。还有一个非常酷的加密匿名层，可以防止 airdrop 交易直接与 GitHub 用户 [Goosig](https://github.com/handshake-org/goosig#goosig) 联系起来。

现在长话短说，我们将创建全新的 GPG 键，制作我们自己的树，在 regtest 模式下将根插入 hsd，并兑换一个 airdrop！

让我们生成一个新的 GPG 私钥，并[将其导出到本地文件](https://github.com/pinheadmz/hs-airdrop/commit/212b9de41d8bbd94046f4135ebdd205609a72819):

```
$ gpg --quick-generate-key Alice rsa4096
$ gpg --armor --output alice_pub.asc --export Alice
$ gpg --armor --output alice_prv.asc --export-secret-keys Alice
```

接下来，我们将使用 bcrypto 中的 PGP 模块来[从 ASCII 编码的文件中提取原始公钥](https://github.com/pinheadmz/hs-airdrop/blob/212b9de41d8bbd94046f4135ebdd205609a72819/regtest/pgp/extract-publickey)，并返回一个 base64 字符串，就像 GitHub 的用户 API 一样:

```
$ ./extract-publickey alice_pub.ascKey ID:
351d404712461946Public key (base64):
BF1keZABEAD2++nDebPDr9M4HpJB+cf5Sn6GEXOej8PZ6D3k3wY3MQxlNkBW6caADsMVsDT1hSNJPzUVY+p+cRA5Aou34NWvRd+OtDDJyHfXy9tbMCMxjV0FDweMfGWinbhfV3WKTl6xecKNBapBzgyKz5ZJK048U/zPXd8cEihmEPsnMiHno8m3uH2XQSusWp6SqYKvBh6Bi3zuWIx7uzuRyS1pwOOe7Go76bU189O7OA9S2rh/6xhX2Fp0WyJiGds7L2Fy1+e43YHB529HhYhFw8jPmPHc1HVP19OSW5YT4BnZDfkhydPLICZwrwYpNxnB/mj7mEu447+/ahFBV7YnH2KBmk/4azePkk1ItqvVP9I1TEYrF7H5wtYsSmqK7/LAG/18sUI17d8F40ZlsPvWq5yr67AikbMaItrSq+eoedbZPWB+AQk1kKCXG1MEVLH3zejHboxXVzqAOen7shrG0TGfjn6N48pLJnkCqOtmI5XMkSibKT1JWH1Vgmm0Z36XwSaeG+d4ryCnZDsPhb3d3UPvvvAy3BHGAyyV8VqCWnmQaqKbLgVnltY8omDIwkz9AYs/d7rhP8Ld6DGbrkcZMNbxu8idwjokhiHr+VBGgkWJxQdfT/ndwIbWedpSAF+19MuhfM9x2VuIEpAYGmA1PEKt6r4+XM3qnA4D+/39n1UAYRFIuQARAQAB
```

[我们还可以生成一个水龙头地址](https://github.com/pinheadmz/hs-airdrop/commit/3156398c6e6ff34c252b8b5c9aacaa79ffd8e0a9)。水龙头地址和空投键被嵌入到两个独立的树中，但它们是链接的，因为重复的用户被删除了。两个树根都在 hsd 客户端中提交。

```
$ faucet-tool createaddress -n regtest...

Address:
rs1qk2hh6xrmmnpsc8pt08tr8uwdqdxp0yuyv6hzsu
```

好吧！这就足够入门了。请记住，我们还向其他一些来源查询了空投中包含的密钥，包括[信任网“强集”](https://en.wikipedia.org/wiki/Web_of_trust#Strong_set)和黑客新闻用户。除了 GPG 密钥，SSH 密钥也是从 GitHub 上刮下来的。

为了构建一个握手空投树，[我们需要创建一些 json 文件](https://github.com/pinheadmz/hs-airdrop/commit/0b9726cc936d88c500dddbe9ef948c1eb05d8a3b)。有几个我们将留空，但它们仍然是必需的，否则脚本会抛出:

```
$ echo "[]" > sponsors.json
$ echo "[]" > creators.json
$ echo "[]" > hn-keys.json
$ echo "" > strongset.asc
```

我们没有任何 SSH 密钥，但是我们仍然需要为我们的 GitHub 用户“Alice”填充一个条目，她有一个 GPG 密钥:

**github-ssh.json**

```
[
  [
    1,          // GitHub User ID #
    "Alice",    // GitHub username
    []          // No SSH keys for Alice
  ]
]
```

最后，我们可以用实际的钥匙做点什么:

**github-pgp.json**

```
[
  [
    1,        // GitHub User ID #
    "Alice",  // GitHub username
    [
      [
        1,                                // User ID #
        -1,                               // Parent Key ID
        "351d404712461946",               // Key ID
        "BF1keZABEAD2++nDebPDr9M4Hp...",  // Raw key as PGP packet
        [["[alice@alice.com](mailto:alice@alice.com)", 1]]          // Email, verified (bool)
      ]
    ]
  ]
]
```

最后，我们将添加我们生成的水龙头地址，并将其分配给不同的用户名和电子邮件，这样它就不会被重复删除。

**水龙头. json**

```
[
  {
    "email": "[bob@bob.com](mailto:bob@bob.com)",
    "github": "Bob"
    "address": "rs1qk2hh6xrmmnpsc8pt08tr8uwdqdxp0yuyv6hzsu",
    "shares": 1
  }
]
```

# 哈希哈希哈希哈希

让我们运行脚本来生成树，并把根还给我们！这是一个只需要为 mainnet 运行一次的过程。脚本需要 json 文件所在的目录作为参数。[我们从空投树开始:](https://github.com/pinheadmz/hs-airdrop/commit/118ec6a9c08561165814dfdfe04432679b0f9fa0)

```
$ scripts/merkelize-airdrop regtest/jsonValid github users: 1
Valid github keys: 2
Invalid github users: 0
Invalid github keys: 0
Valid strongset members: 0
Invalid strongset members: 0
Valid hackernews users: 0
Invalid hackernews users: 0
Wrote buckets (size=0.0004901885986328125mb).
Wrote merkle tree with 2 keys and 1 leaves.
Checksum: fb92f890ab91ad36dda17f28f114f771ecb76b0cae0b154f2415a4a2b1be68b7
Tree Root: ee8bff349dec24a8378f38fbed35bba5cd0ebde2d5f256101ffbadac93920744
Leaves: 1
Keys: 2
Max Keys: 2
Depth: 0
Subdepth: 1
Faucet: 1
Shares: 1
Reward: 476000000000000
```

注意这个脚本如何在`build/`和`etc/`中生成一些输出。文件`etc/tree.json`实际上将由 merkelize-水龙头脚本导入，否则将包含 256 个随机数文件的校验和，这些随机数文件将在`build/nonces`中输出。这 256 个“桶”最终将包含所有大约 80，000 个抓取的密钥的加密随机数。在 Goosig 构造中使用随机数来创建零知识证明，以保持空投赎回的匿名性。

[现在让我们生成水龙头树根:](https://github.com/pinheadmz/hs-airdrop/commit/8369e3a79072dea781074821948e24a5500c2de7)

```
$ scripts/merkelize-faucet regtest/jsonValid sponsor addresses: 0
Valid creator addresses: 0
Valid participant addresses: 1
Wrote merkle tree with 1 leaves.
Checksum: 06d55ac651d4c5b2264ad62c60c9633079709c9bbfff1ffd6e0172616964be32
Tree Root: 1d592d73c49f0aa50b2ef857c343700be0d00ebe13167a7cebd633d3bae682d8
Leaves: 1
Depth: 0
Participants: 1
Faucet Total: 476000000000000
Shares: 1
Sponsors: 0
Creators: 0
External Total: 0
```

这个脚本还会输出一个文件`build/proof.json`。[你可以在 GitHub 上看到这个文件的 mainnet 版本。](https://github.com/handshake-org/hs-tree-data/blob/master/proof.json)

# 证明它

现在开始变得有趣了。在上面的 json 输出中，我们有两个标记为`Tree Root`的字段。[让我们将这些根插入到 hsd 客户端](https://github.com/pinheadmz/hsd/commit/28dca2c4e05630d7fe01740324660a8a81481a29)中，并在 regtest 模式下运行它(仅限本地)，这样我们就可以兑换 airdrops 了。请记住，这是对共识关键参数的修改！一旦我们做出这一更改，我们的客户端将不会在任何其他网络上同步。我们还需要[修改 hs-airdrop 脚本](https://github.com/pinheadmz/hs-airdrop/commit/2e7e126ab3dacc7566f3a2eb0a97503d8f42393e)，以便根据我们的新数据创建证明。

这是一个水龙头收件人将如何要求他们的硬币。首先，为水龙头地址生成空投证明:

```
$ bin/hs-airdrop rs1qk2hh6xrmmnpsc8pt08tr8uwdqdxp0yuyv6hzsuAttempting to create proof.
This may take a bit...
Creating proof from leaf...JSON:
{
  "index": 0,
  "proof": [],
  "subindex": 0,
  "subproof": [],
  "key": {
    "type": "ADDRESS",
    "version": 0,
    "address": "b2af7d187bdcc30c1c2b79d633f1cd034c179384",
    "value": 476000000000000,
    "sponsor": false
  },
  "version": 0,
  "address": "b2af7d187bdcc30c1c2b79d633f1cd034c179384",
  "fee": 100000000,
  "signature": ""
}Base64 (pass this to $ hsd-rpc sendrawairdrop):
AAAAAAAAACAEABSyr30Ye9zDDBwredYz8c0DTBeThADA6WLrsAEAAAAUsq99GHvcwwwcK3nWM/HNA0wXk4T+AOH1BQA=
```

还有救赎！

```
$ hsd-rpc --network=regtest sendrawairdrop AAAAAAAAACAEABSyr30Ye9zDDBwredYz8c0DTBeThADA6WLrsAEAAAAUsq99GHvcwwwcK3nWM/HNA0wXk4T+AOH1BQA=9a2771031b305533b1015f2b1860079daa6243c873a51abf4735621c4c16007e
```

现在，我们将为“爱丽丝”GPG 密钥生成空投证明。这个调用比较复杂，需要兑换者指定自己的 GPG 私钥文件，[自己的握手钱包地址](https://handshake-org.github.io/api-docs/#generate-receiving-address)，以及一笔矿工费。请注意，水龙头赎回有[硬编码费率](https://github.com/handshake-org/hsd/blob/cc35dc2a351eecd6adfae94b981a2d85277ecbb5/lib/primitives/airdropproof.js#L18-L19)……你能猜到为什么吗？🤔

```
$ bin/hs-airdrop regtest/pgp/alice_prv.asc 351d404712461946 rs1qy9uplxpt5cur32rw3zmyf8e7tp87w8slly2fms 0.5Attempting to create proof.
This may take a bit...
Decrypting nonce...
Finding merkle leaf...
Creating proof from leaf...JSON:
{
  "index": 0,
  "proof": [],
  "subindex": 1,
  "subproof": [
  "9de514887afa96c585e8a27161c7bba420703e25a280aa67c57ffc8980816311"
  ],
  "key": {
    "type": "GOO",
    "C1": "0f6d9..."
  },
  "version": 0,
  "address": "21781f982ba63838a86e88b6449f3e584fe71e1f",
  "fee": 500000,
  "signature": "4933fd82aa8aa7fb900a2..."
}Base64 (pass this to $ hsd-rpc sendrawairdrop):
AAAAAAABAZ3lFIh6+pbFheiicWHHu6QgcD4looCqZ8V//ImAgWMR/QEBA...
```

赎回来！

```
$ hsd-rpc --network=regtest sendrawairdrop AAAAAAABAZ3lFIh6+pb...
```

# 确认

关于空投证明要注意的最后一件奇怪的事情是，它们并不是真正的交易。它们被广播、转发并存储在矿工的内存池中，但与普通交易的方式不同。事实上，空投证明作为额外的输入(包含见证中的证明数据)和输出被添加到块的 coinbase 事务中。现在，我们已经“发送”了两个空投证明，让我们生成一个块，并验证我们确实凭空创造了钱:

```
$ hsd-rpc --network=regtest generatetoaddress 1 rs1q30ppv5gyrwpy4wyk0v6uzawxygdtvrpux8yrg2[
  "022882f6566671742c524cb485109a4d8ef51861e09f286d651d927cfe6630ee"
]$ hsd-cli --network=regtest block 022882f6566671742c524cb485109a4d8ef51861e09f286d651d927cfe6630ee...
"outputs": [
    {
      "value": 2100500000,
      "address": "rs1q30ppv5gyrwpy4wyk0v6uzawxygdtvrpux8yrg2",
      "covenant": {
        "type": 0,
        "action": "NONE",
        "items": []
      }
    },
    {
      "value": 475999900000000,
      "address": "rs1qk2hh6xrmmnpsc8pt08tr8uwdqdxp0yuyv6hzsu",
      "covenant": {
        "type": 0,
        "action": "NONE",
        "items": []
      }
    },
    {
      "value": 4370322008,
      "address": "rs1qy9uplxpt5cur32rw3zmyf8e7tp87w8slly2fms",
      "covenant": {
        "type": 0,
        "action": "NONE",
        "items": []
      }
    }
  ]
...
```

看那个！一个有三个输出的 coinbase 事务！第一个是矿工的补贴和奖励，另外两个是*免费的钱*空投输出给开源社区的成员！

# 保留名称

记住握手时的名字就像硬币一样存储在 UTXOs 中。因此，为了让排名前 10 万的网站在握手区块链上获得自己的名字，他们需要将其与他们控制的握手钱包地址相关联。然后，他们必须向网络提交所有权证明，这将允许他们生成一个 UTXO，将他们的名字锁定到他们的私钥(或赎回脚本)。

这个所有权证明是基于一个名为 [DNSSEC](https://en.wikipedia.org/wiki/Domain_Name_System_Security_Extensions) 的 DNS 记录子集。DNSSEC 记录不是通常的`name -> IP address`类型的 DNS 记录。相反，它们像区域的公钥一样存储数据。然后，这些密钥可用于签署和验证该区域中的其他记录。它们也可以用于签署属于*子*区域的公钥。你知道这是怎么回事吗？

DNSSEC 从一个单一的中央密钥开始，这个密钥是由 ICANN 在一个[精心制作的、超正式的、高度审查的仪式中生成的](https://www.iana.org/dnssec/ceremonies/33)(这正是握手旨在颠覆和分散的东西！).这个密钥就是**根区域密钥签名密钥，**我们都应该在它令人敬畏的力量面前颤抖。ICANN KSK 广为人知，[存储在](https://github.com/chjj/bns/blob/master/lib/constants.js#L984-L985)我们自己的 DNS 解析器包 [bns](https://github.com/chjj/bns) 中。

ICANN 使用这个密钥来签署根区域文件[，其中包括对顶级域名`.com`的 KSK 的承诺。反过来，`.com`域的管理者使用他们的密钥为像`google.com`和`bitcoin.com`这样的域签署 ksk。最后，*那些*域管理员在他们自己的区域中签署记录，包括域名服务器、地址和文本记录。](https://en.wikipedia.org/wiki/Zone_file)

因此，如果`bitcoin.com`的所有者想要在握手时声明他们的名字，我们期望看到这个签名链:*“众所周知的* `*root*` *区域密钥签署一个* `*.com*` *区域密钥，该区域密钥签署一个* `*bitcoin.com*` *密钥，该密钥签署一个握手地址。”*

通常，[握手名称声明脚本](https://github.com/chjj/bns/blob/master/bin/bns-prove)会进行实际的 DNS 查询，以获得生成证明所需的密钥链。但是，既然我们要一起黑掉我们自己的 DNSSEC 连锁店，我们就必须在方法上更有创造性。

# 看看我，我现在是 ICANN 了

在接下来的演示中，我们将使用[绑定](https://www.isc.org/bind/)，我可以用`brew install bind`将它安装在 OSX 上。这将给我们三个我们需要继续的实用程序:`[dnssec-keygen](https://linux.die.net/man/8/dnssec-keygen)`、`[dnssec-dsfromkey](https://linux.die.net/man/8/dnssec-dsfromkey)`和`[dnssec-signzone](https://linux.die.net/man/8/dnssec-signzone)`。

首先，我们将使用握手钱包来生成对域名`bitcoin`的声明。该命令将生成一个 TXT 记录，其中包括钱包地址和一些其他元数据:

```
$ hsw-rpc --network=regtest createclaim bitcoin{
  "name": "bitcoin",
  "target": "bitcoin.com.",
  "value": 566471548,
  "size": 5120,
  "fee": 25600,
  "address": "rs1q7ywfylsgt3fj4n6fz7q8k8j0wz43lcws2ee2ya",
  "txt": "hns-regtest:aakpchespyefyuzkz5erpad3dzhxbky74hip2adei6z7736u6qcacthqpeg2ncko7nnwpepv76f6sn3ywjlerdorw7dacaaaadrg5xku"
}
```

现在我们将创建`bitcoin.com`区域文件。首先生成两个密钥:一个区域签名密钥(ZSK)和一个密钥签名密钥(KSK)。无论在哪里运行，这些命令都将输出公钥和私钥文件:

```
$ dnssec-keygen -f KSK -a RSASHA256 -n ZONE bitcoin.comGenerating key pair...
Kbitcoin.com.+008+37177$ dnssec-keygen -a RSASHA256 -n ZONE bitcoin.comGenerating key pair...
Kbitcoin.com.+008+27510
```

现在创建一个区域文件模板，包括密钥和握手证明:

**bitcoin.com.zone**

```
$ORIGIN bitcoin.com.
$TTL 172800
@ IN SOA com. admin.email ( 2007120710 1d 2h 4w 1h )$INCLUDE /work/guide/keys/Kbitcoin.com.+008+37177.key
$INCLUDE /work/guide/keys/Kbitcoin.com.+008+27510.keybitcoin.com. IN TXT "hns-regtest:aakpchespyefyuzkz5erpad3dzhxbky74hip2adei6z7736u6qcacthqpeg2ncko7nnwpepv76f6sn3ywjlerdorw7dacaaaadrg5xku"
```

签署区域文件:

```
$ dnssec-signzone -o bitcoin.com bitcoin.com.zone
```

这将生成一个包含签名记录的新文件`bitcoin.com.zone.signed`，如下所示:

```
172800 TXT "hns-regtest:aakpchespyefyuzkz5erpad3dzhxbky74hip2adei6z7736u6qcacthqpeg2ncko7nnwpepv76f6sn3ywjlerdorw7dacaaaadrg5xku"
172800 RRSIG TXT 8 2 172800 (
     20190926203106 20190827203106 17421 bitcoin.com.
     wE/Ky5yH9bG2DAmOp1d0oSOA2OpsrmxQjv8w
     OvnNkYC65Vo38eBDj64wSU2x1tgO91TBepj1
     rLj3Df2owMYv3A+Ciu/qXXCRSM/2krcltXis
     guV7tLltBQLsiPtVs1wEr8vCWcaZP4yd1lo4
     1Bmvi0YajOsxV7NNWJloTPJfdNM= )
```

现在我们需要以类似的方式再创建两个区域文件(用于`.com`和`root`)。每个父区域需要包括其子区域的 KSK 的 [ds 记录](https://www.cloudflare.com/learning/dns/dns-records/dnskey-ds-records/)(即`.com`区域需要`bitcoin.com`键的 DS 记录)。我们像这样创建该记录，并将其添加到 **com.zone** 模板文件中:

```
$ dnssec-dsfromkey -2 Kbitcoin.com.+008+37177.keybitcoin.com. IN DS 17421 8 2 78D60AF13E97693AC2F32B591FD3D60D34E151016F8692475617FBCD03CDCBE1
```

我们将重复生成密钥的过程，将它们添加到[区域文件](https://github.com/pinheadmz/hsd-regtest-claim/tree/master/zone-files)，并且[为`.com`和`root`区域文件签署文件](https://github.com/pinheadmz/hsd-regtest-claim/tree/master/signed-zone-files)。

最后，我们将一起粉碎所有三个区域文件。我们不需要包括像 SOA 和 NSEC 这样的每个区域的所有记录，只需要包括 ds、DNSKEY、TXT 和 RRSIG。你可以在这里看到我完整的所有权证明。

当然，为了使这些有效，我们需要在 Handshake 中插入我们随意自制的根区域 KSK。[我们通过生成一个 DS 记录并将其添加到 bns](https://github.com/pinheadmz/bns/commit/df201590b013849e853aafef9aaa6905ad954fb6) 中的信任锚来实现这一点(bns 是 hsd 的一个依赖项，因此它位于我们的`node_modules`目录中)。

# 要求那个名字

好了，你准备好拥有握手时的名字`bitcoin`了吗？我们还有一个小技巧要完成:我们需要将证明编译成 base64 字符串。这可以通过使用 bns 在 nodejs REPL 中使用两行代码来完成:

```
$ node> const bns = require('bns')undefined> bns.Ownership.Proof.fromString(
    fs.readFileSync('/work/guide/zones/ownership-bitcoin.zone')
    .toString('ascii')
  ).toBase64()'AwMAADAAAQACowAAiAEAAwgDAQABnt9Z4x3v0DvnkBY0Hx17RTi3Ac...'
```

现在，我们可以使用那个巨大的 base64 blob(包含整个 DNSSEC 签名系列)来声明我们的链上保留名称:

```
hsd-rpc --network=regtest sendrawclaim AwMAADAAAQACowAAiAEAAwgD...
```

# 确认

与 airdrop 赎回一样，保留名称声明也包含在块的 coinbase 事务中:

```
$ hsd-rpc --network=regtest generatetoaddress 1 rs1q30ppv5gyrwpy4wyk0v6uzawxygdtvrpux8yrg2[
  "908eb35fab448ec928126dc7b451b27798b5f611bc50b5bfc081a7d18add04e0"
]$ hsd-cli --network=regtest block 908eb35fab448ec928126dc7b451b27798b5f611bc50b5bfc081a7d18add04e0...
  "outputs": [
    {
      "value": 2000025600,
      "address": "rs1q30ppv5gyrwpy4wyk0v6uzawxygdtvrpux8yrg2",
      "covenant": {
        "type": 0,
        "action": "NONE",
        "items": []
      }
    },
    {
      "value": 566445948,
      "address": "rs1q7ywfylsgt3fj4n6fz7q8k8j0wz43lcws2ee2ya",
      "covenant": {
        "type": 1,
        "action": "CLAIM",
        "items": [
"f82f54fe3a9daa86316dc706e74b31d57ce6b21a12104cdfbd3f90b627847105",
          "0c000000",
          "626974636f696e",
          "01",          "47b3ffefd4f404014cf0790da6894efb5b6791f5ff8be93778b256488dd1b7c6",
          "01000000"
        ]
      }
    }
  ]
...
```

瞧啊。第二个事务输出是我们的`CLAIM`，它将`bitcoin`名称的控制权分配给我们生成的钱包地址。我们能够通过伪造`.com`和`root`区域的密钥，并在 regtest 模式下恶意地将它们插入我们自己的 hsd 客户端来实现这一点。

# 🍺结论🍺

你有没有弄清楚为什么水龙头证明有硬编码的费用？第一个在`matthew@purse.io`将正确答案发邮件给我的人，我会给你 5 美元的比特币。如果在握手 mainnet 启动之前我没有得到正确的答案，我可以用 [dollarydoos](https://github.com/handshake-org/hsd/blob/master/lib/protocol/consensus.js#L25) 送你 5 美元！

*本次演示使用的文件&已修改，原件:*

**hs-airdrop** : ( [原](https://github.com/handshake-org/hs-airdrop) ) ( [改](https://github.com/pinheadmz/hs-airdrop/commits/guide) )
用于生成空投树，为钥匙持有者和水龙头接收者创建凭证，以在区块链领取他们的硬币。

**hsd:** ( [原创](https://github.com/handshake-org/hsd) ) ( [修改](https://github.com/pinheadmz/hsd/commit/28dca2c4e05630d7fe01740324660a8a81481a29) )
握手参考客户。验证区块链并审核所有交易的协议合规性。

**bns:** ( [原](https://github.com/chjj/bns) ) ( [修改](https://github.com/pinheadmz/bns/commit/df201590b013849e853aafef9aaa6905ad954fb6))
node . js 的递归 DNS 服务器和解析器，作为 hsd 的依赖项包含在内，是处理 DNSSEC 所必需的。

**HSD-regtest-claim:**([原件仅用于本指南](https://github.com/pinheadmz/hsd-regtest-claim) )
用于为保留名称声明创建“有效”DNSSEC 证明的密钥和区域文件。

**龙头-工具:** ( [原文](https://github.com/handshake-org/faucet-tool) )
生成 BIP39 助记短语、私钥和 mainnet hns 地址。用于申请水龙头空投。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)