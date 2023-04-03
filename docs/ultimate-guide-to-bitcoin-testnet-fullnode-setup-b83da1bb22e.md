# 比特币测试网 Fullnode 设置终极指南

> 原文：<https://medium.com/coinmonks/ultimate-guide-to-bitcoin-testnet-fullnode-setup-b83da1bb22e?source=collection_archive---------1----------------------->

![](img/417fbab473ee1b207bb94f1a0f0b1fed.png)

Blockchain Image from Unsplash.com

在这篇深入的教程中，我们将讲述**的**基础知识** **，为什么您想要一个比特币全节点，如何**使用 **testnet 链**在 linux 上设置一个比特币全节点**，以及如何使用 **cli** 和 **json rpc** 与您的节点和区块链交互，在这里我们将创建钱包和地址并发送**tbtc******

# 区块链基础知识

在我们开始设置我们的比特币全节点之前，我们首先需要了解一些区块链的基础知识，如果你已经知道，你可以跳过这篇文章的设置部分。

## 街区

交易数据被永久地记录在称为块的文件中。你可以把它想象成一个交易账本。随着时间的推移，块被组织成线性序列。

新的事务不断地被矿工处理成新的块，这些新的块被添加到链的末端。随着区块在区块链中埋得越来越深，它们变得越来越难改变或移除，这就产生了 [**比特币的不可逆交易**](https://en.bitcoin.it/wiki/Irreversible_Transactions) 。

添加到区块链的第一块被称为 [**创世纪块**](https://en.bitcoin.it/wiki/Genesis_block)

## 区块链

区块链是由基于比特币协议的系统中的所有节点共享的交易数据库。一份**货币区块链的完整副本**包含了该货币曾经执行的每一笔交易**。有了这些信息，人们就可以知道在任何历史时刻每个地址的价值。**

每个块都包含前一个块的散列。这具有创建从**起源块**到当前块的块链的效果。每个数据块按时间顺序肯定在前一个数据块之后，因为前一个数据块的哈希是未知的。一旦每个块在链中存在一段时间，对其进行修改在计算上也是不切实际的，因为在它之后的每个块也必须重新生成。这些特性使得比特币交易不可逆转。区块链是比特币的主要创新。

## 采矿

挖掘是**将交易记录添加到比特币过去交易的公共账本**中的过程。术语“*采矿钻机*”是指为“*采矿*”执行必要计算的单个计算机系统。

区块链用于向网络的其余部分确认交易已经发生。比特币节点使用区块链来区分合法的比特币交易和试图重新花费已经在其他地方花费的硬币。

## 结节

任何一台**将**连接到**比特币网络**的电脑都被称为**节点**。完全验证比特币所有规则的节点称为完全节点。最流行的全节点软件实现叫做比特币核心，其版本可以在他们的 [**github 页面**](https://github.com/bitcoin/bitcoin/releases) 找到

## 什么是完整节点

完整节点是一个节点(运行比特币内核的计算机系统)，它下载每个区块和交易**，并根据比特币的**共识规则**对其进行**检查。它完全验证事务和块。几乎所有的完整节点还通过接受来自其他完整节点的事务和块来帮助网络，**验证**这些事务和块，然后将它们转发给其他完整节点。****

**共识规则**的一些**例子**:

*   积木可能只能 [**制造**](https://en.bitcoin.it/wiki/Controlled_supply) 一定数量的比特币。(目前每块 6.25 BTC。)
*   交易必须有正确的比特币签名。
*   交易/区块必须采用正确的数据格式。
*   在单个 [**区块链**](https://en.bitcoin.it/wiki/Block_chain) 内，一个事务输出不能被双重花费。

在**最小值**时，完整节点**必须**下载已经发生的每个事务、所有新事务和所有块头。此外，满节点必须存储关于每个未用完的事务输出的信息，直到它被用完。

默认情况下，完整节点的效率低下，因为它们至少下载每个新事务两次，并且它们永久存储整个区块链(截至 20180214 年超过 165 GB)，即使只有未使用的事务输出(<2 GB) are required. Performance can improved by enabling [**)-阻塞地**](https://bitcointalk.org/index.php?topic=1377345.0) 模式并启用 [**修剪**](https://bitcoin.org/en/release/v0.12.0#wallet-pruning)

## 归档节点

完整节点的子集也接受传入连接，并将旧块上载到网络上的其他对等节点。如果软件默认以“listen=1”运行，则会出现这种情况。

与一些流行的误解相反，成为归档节点并不是成为完整节点的必要条件。如果用户的带宽受限，他们可以使用`-listen=0 `，如果他们的磁盘空间受限，他们可以使用修剪，同时仍然是一个完全验证节点，负责强制执行比特币的一致性规则，并为比特币的整体安全性做出贡献。

大部分信息都来自[这个](https://en.bitcoin.it/wiki/Full_node)维基。

# 设置比特币核心

现在有趣的部分是，为了在 linux 上安装比特币核心，在我的例子中，我使用了一个全新的 Ubuntu 20.04 实例，下面的命令将以非根用户的身份执行。

## 环境变量

设置以下环境变量，可在:[https://bitcoincore.org/bin/](https://bitcoincore.org/bin/)检索到‘bit coin _ version’的最新版本，执行以下操作:

```
$ export ARCH=x86_64
$ export BITCOIN_VERSION=0.21.1
$ export BITCOIN_URL=[https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/bitcoin-${BITCOIN_VERSION}-${ARCH}-linux-gnu.tar.gz](https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/bitcoin-${BITCOIN_VERSION}-${ARCH}-linux-gnu.tar.gz)
$ export BITCOIN_DATA_DIR=/blockchain/bitcoin/data
```

## 装置

为比特币创建用户和组:

```
$ sudo groupadd -r bitcoin
$ sudo useradd -r -m -g bitcoin -s /bin/bash bitcoin
```

更新包管理器并安装依赖项:

```
$ sudo apt update 
$ sudo apt install ca-certificates gnupg gpg wget jq --no-install-recommends -y
```

下载比特币核心，并验证包是否与 sha 哈希匹配:

```
$ cd /tmp
$ wget [https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/SHA256SUMS.asc](https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/SHA256SUMS.asc)
$ wget -qO bitcoin-${BITCOIN_VERSION}-${ARCH}-linux-gnu.tar.gz “${BITCOIN_URL}”
$ cat SHA256SUMS.asc | grep bitcoin-${BITCOIN_VERSION}-${ARCH}-linux-gnu.tar.gz | awk ‘{ print $1 }’
```

将软件包解压缩到`/opt/bit coin/$ { bit coin _ VERSION } '，并排除任何图形用户界面二进制文件，创建主目录并设置所有权:

```
$ sudo mkdir -p /opt/bitcoin/${BITCOIN_VERSION}
$ sudo mkdir -p ${BITCOIN_DATA_DIR}
$ sudo tar -xzvf bitcoin-${BITCOIN_VERSION}-${ARCH}-linux-gnu.tar.gz -C /opt/bitcoin/${BITCOIN_VERSION} — strip-components=1 — exclude=*-qt
$ sudo ln -s /opt/bitcoin/${BITCOIN_VERSION} /opt/bitcoin/current
$ sudo rm -rf /tmp/*
$ sudo chown -R bitcoin:bitcoin ${BITCOIN_DATA_DIR}
```

将来必须升级比特币核心的软件版本时，可以删除带有“sudo RM-RF/usr/local/bit coin/current”和符号链接的符号链接，使其升级到如上所示的较新版本。

## 配置

创建比特币配置，在这里您可以看到我正在使用 testnet 链，由于我的使用情形的存储限制，我正在将“修剪”模式设置为 1GB，如果不设置“BITCOIN_RPC_USER”，它将使用用户“bitcoin”，如果不设置“BITCOIN_RPC_PASSWORD”，它将为 json-rpc 接口生成密码:

```
$ cat > bitcoin.conf.tmp << EOF
datadir=${BITCOIN_DATA_DIR}
printtoconsole=1
rpcallowip=127.0.0.1
rpcuser=${BITCOIN_RPC_USER:-bitcoin}
rpcpassword=${BITCOIN_RPC_PASSWORD:-$(openssl rand -hex 24)}
testnet=1
prune=1000
[test]
rpcbind=127.0.0.1
rpcport=18332
EOF
```

为 bitcoind 创建 systemd 单位文件:

```
$ cat > bitcoind.service << EOF
[Unit]
Description=Bitcoin Core Testnet
After=network.target[Service]
User=bitcoin
Group=bitcoin
WorkingDirectory=${BITCOIN_DATA_DIR}Type=simple
ExecStart=/usr/local/bitcoin/current/bin/bitcoind -conf=$BITCOIN_DATA_DIR/bitcoin.conf[Install]
WantedBy=multi-user.target
EOF
```

现在移动临时配置文件，更改所有权，并将比特币主目录符号链接到我们之前创建的路径:

```
$ sudo mv bitcoin.conf.tmp $BITCOIN_DATA_DIR/bitcoin.conf
$ sudo chown bitcoin:bitcoin $BITCOIN_DATA_DIR/bitcoin.conf
$ sudo chown -R bitcoin $BITCOIN_DATA_DIR
$ sudo ln -sfn $BITCOIN_DATA_DIR /home/bitcoin/.bitcoin
$ sudo chown -h bitcoin:bitcoin /home/bitcoin
$ sudo chown -R bitcoin:bitcoin /home/bitcoin
```

将 systemd 单位文件移动到位，然后重新加载 systemd 并启动比特币:

```
$ sudo mv bitcoind.service /etc/systemd/system/bitcoind.service
$ sudo systemctl daemon-reload
$ sudo systemctl enable bitcoind
$ sudo systemctl start bitcoind
```

切换到“比特币”用户:

```
$ sudo su — bitcoin
```

并将比特币路径附加到` ~/.profile `文件中，以便您的用户知道在哪里可以找到比特币二进制文件:

```
$ export PATH=$PATH:/opt/bitcoin/current/bin
```

# 初始块下载

bitcoind 进程启动后，初始块下载将开始，您可以作为比特币用户使用 cli 了解进度:

```
$ bitcoin-cli -getinfo
```

或者作为根用户，您可以使用“journalctl”查看进度:

```
$ sudo journalctl -fu bitcoind
Sep 03 08:44:23 rpi-01 bitcoind[532]: 2021–09–03T06:44:23Z UpdateTip: new best=x height=2091205 version=0x20c00000 log2_work=74.461678 tx=60938712 date=’2021–09–03T06:44:13Z’ progress=1.000000 cache=1.7MiB(13673txo)
```

## 解决纷争

如果遇到任何问题，您可以使用以下命令查看 bitcoind 的状态:

```
$ sudo systemctl status bitcoind
```

或者检查日志:

```
$ sudo journalctl -fu bitcoind
```

# CLI 用法

要获取当前块数:

```
$ bitcoin-cli getblockcount
2091215
```

获得一些关于比特币区块链上第一个区块的基本信息。作为创世块，它的索引值为 0。我们可以使用“getblockhas”来获取曾经创建的第一个块的哈希值:

```
$ bitcoin-cli getblockhash 0
000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943
```

我们现在可以使用带有哈希值的“getblock”来检索块的详细信息:

```
$ bitcoin-cli getblock 000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943
{
 “hash”: “000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943”,
 “confirmations”: 2004218,
 “strippedsize”: 285,
 “size”: 285,
 “weight”: 1140,
 “height”: 0,
 “version”: 1,
 “versionHex”: “00000001”,
 “merkleroot”: “4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b”,
 “tx”: [
 “4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b”
 ],
 “time”: 1296688602,
 “mediantime”: 1296688602,
 “nonce”: 414098458,
 “bits”: “1d00ffff”,
 “difficulty”: 1,
 “chainwork”: “0000000000000000000000000000000000000000000000000000000100010001”,
 “nTx”: 1,
 “nextblockhash”: “00000000b873e79784647a6c82962c70d228557d24a747ea4d1b8bbe878e1206”
}
```

我们可以使用“nextblockhash”来检索关于块 1 的信息:

```
$ bitcoin-cli getblock 00000000b873e79784647a6c82962c70d228557d24a747ea4d1b8bbe878e1206
{
 “hash”: “00000000b873e79784647a6c82962c70d228557d24a747ea4d1b8bbe878e1206”,
 “confirmations”: 2004217,
 “strippedsize”: 190,
 “size”: 190,
 “weight”: 760,
 “height”: 1,
 “version”: 1,
 “versionHex”: “00000001”,
 “merkleroot”: “f0315ffc38709d70ad5647e22048358dd3745f3ce3874223c80a7c92fab0c8ba”,
 “tx”: [
 “f0315ffc38709d70ad5647e22048358dd3745f3ce3874223c80a7c92fab0c8ba”
 ],
 “time”: 1296688928,
 “mediantime”: 1296688928,
 “nonce”: 1924588547,
 “bits”: “1d00ffff”,
 “difficulty”: 1,
 “chainwork”: “0000000000000000000000000000000000000000000000000000000200020002”,
 “nTx”: 1,
 “previousblockhash”: “000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943”,
 “nextblockhash”: “000000006c02c8ea6e4ff69651f7fcde348fb9d557a06e6957b65552002a7820”
}
```

我们可以使用`-getinfo '来获取显示验证进度和钱包余额的信息，如果它们存在的话:

```
$ bitcoin-cli -getinfo
{
 “version”: 210100,
 “blocks”: 2091215,
 “headers”: 2091215,
 “verificationprogress”: 0.9999993040419591,
 “timeoffset”: 0,
 “connections”: {
 “in”: 0,
 “out”: 10,
 “total”: 10
 },
 “proxy”: “”,
 “difficulty”: 16777216,
 “chain”: “test”,
 “relayfee”: 0.00001000,
 “warnings”: “Warning: unknown new rules activated (versionbit 28)”,
 “balances”: {
 }
}
```

查看更多示例 [**chainquery**](https://chainquery.com/bitcoin-cli)

# JSON RPC 用法

首先获取 jsonrpc 用户和密码:

```
$ cat ~/.bitcoin/bitcoin.conf | grep -E ‘(rpcuser|rpcpassword)’
rpcuser=bitcoin
rpcpassword=xxxxxxxxxxxxxx
```

因为这是一个测试环境，我们可以将用户名和密码设置为一个环境变量:

```
# the user and password might differ on your setup
$ export bitcoinauth=”bitcoin:xxxxxxxxxxxxxx”
```

## 钱包互动

创建钱包:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “createwallet”, “params”: [“wallet”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/](http://127.0.0.1:18332/)
{“result”:{“name”:”wallet”,”warning”:””},”error”:null,”id”:”curltest”}
```

列出钱包:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “tutorial”, “method”: “listwallets”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/](http://127.0.0.1:18332/)
{“result”:[“wallet”],”error”:null,”id”:”tutorial”}
```

如果我们检查“bitcoin.conf ”,我们会注意到我们的配置中没有加载钱包:

```
$ cat ~/.bitcoin/bitcoin.conf | grep -c ‘wallet=’
0
```

但是由于我们创建了 wallet，我们可以在我们的数据目录中看到“wallet.dat ”:

```
$ find /blockchain/ -type f -name wallet.dat
/blockchain/bitcoin/data/testnet3/wallets/wallet/wallet.dat
```

让我们重新启动“bitcoind”服务，看看我们是否还能列出我们的钱包，首先重新启动该服务:

```
$ sudo systemctl restart bitcoind
```

然后等待几秒钟，列出钱包:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “tutorial”, “method”: “listwallets”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/](http://127.0.0.1:18332/)
{“result”:[],”error”:null,”id”:”tutorial”}
```

如你所见，它没有被加载，因为它不在配置中。让我们更新配置` ~/。bitcoin/bitcoin.conf `，并重新启动服务:

```
# more wallets can be referenced by using another wallet= config
[test]
wallet=wallet
# which corresponds to datadir + walletdir
# /blockchain/bitcoin/data/testnet3/wallets/wallet/wallet.dat
# /blockchain/bitcoin/data/testnet3/wallets/wallet/db.log
```

当我重新启动“bitcoind”服务时，我使用“journalctl -fu bitcoind”检查日志，我可以看到钱包已被加载:

```
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z init message: Loading wallet…
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z BerkeleyEnvironment::Open: LogDir=/blockchain/bitcoin/data/testnet3/wallets/wallet/database ErrorFile=/blockchain/bitcoin/data/testnet3/wallets/wallet//db.log
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z txindex thread start
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z txindex is enabled at height 2004267
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z txindex thread exit
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z [wallet] Wallet File Version = 169900
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z [wallet] Keys: 2001 plaintext, 0 encrypted, 2001 w/ metadata, 2001 total. Unknown wallet records: 0
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z [wallet] Wallet completed loading in 39ms
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z init message: Rescanning…
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z [wallet] Rescanning last 2 blocks (from block 2004265)…
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z [wallet] Rescan started from block 000000000000001f778a8e2b68cf05490ae000e653b925bb0552c1b79ef4fe70…
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z [wallet] Rescan completed in 2ms
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z [wallet] setKeyPool.size() = 2000
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z [wallet] mapWallet.size() = 0
Jun 10 13:16:23 ip-172–31–82–15 bitcoind[41053]: 2021–06–10T13:16:23Z [wallet] m_address_book.size() = 0
```

当我再次列出钱包时:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “tutorial”, “method”: “listwallets”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/](http://127.0.0.1:18332/)
{“result”:[“wallet”],”error”:null,”id”:”tutorial”}
```

既然钱包已经加载，我们可以获取钱包信息:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “tutorial”, “method”: “getwalletinfo”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/](http://127.0.0.1:18332/) | jq .
{
 “result”: {
 “walletname”: “wallet”,
 “walletversion”: 169900,
 “format”: “bdb”,
 “balance”: 0,
 “unconfirmed_balance”: 0,
 “immature_balance”: 0,
 “txcount”: 0,
 “keypoololdest”: 1623329789,
 “keypoolsize”: 1000,
 “hdseedid”: “x”,
 “keypoolsize_hd_internal”: 1000,
 “paytxfee”: 0,
 “private_keys_enabled”: true,
 “avoid_reuse”: false,
 “scanning”: false,
 “descriptors”: false
 },
 “error”: null,
 “id”: “tutorial”
}
```

创建另一个名为“test-wallet”的 wallet:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “createwallet”, “params”: [“test-wallet”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/](http://127.0.0.1:18332/)
{“result”:{“name”:”test-wallet”,”warning”:””},”error”:null,”id”:”curltest”}
```

创建钱包后，再次列出钱包:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “listwallets”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/](http://127.0.0.1:18332/)
{“result”:[“wallet”,”test-wallet”],”error”:null,”id”:”curltest”}
```

现在我们有了两个钱包，当我们想对一个特定的钱包执行“getwalletinfo”方法时，我们需要指定钱包的名称，在本例中为“test-wallet ”:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getwalletinfo”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet) | jq .
{
 “result”: {
 “walletname”: “test-wallet”,
 “walletversion”: 169900,
 “format”: “bdb”,
 “balance”: 0,
 “unconfirmed_balance”: 0,
 “immature_balance”: 0,
 “txcount”: 0,
 “keypoololdest”: 1623333495,
 “keypoolsize”: 1000,
 “hdseedid”: “x”,
 “keypoolsize_hd_internal”: 1000,
 “paytxfee”: 0,
 “private_keys_enabled”: true,
 “avoid_reuse”: false,
 “scanning”: false,
 “descriptors”: false
 },
 “error”: null,
 “id”: “curltest”
}
```

为了了解“测试钱包”的数据存放位置，我们可以使用“find ”:

```
$ find /blockchain/ -type f -name wallet.dat | grep test-wallet
/blockchain/bitcoin/data/testnet3/wallets/test-wallet/wallet.dat$ find /blockchain/ -type f -name db.log | grep test-wallet
/blockchain/bitcoin/data/testnet3/wallets/test-wallet/db.log
```

在` ~/的配置中包含 wallet 名称。bitcoin/bitcoin.conf `:

```
[test]
wallet=wallet
wallet=test-wallet
```

然后重新启动 bitcoind:

```
$ sudo systemctl restart bitcoind
```

现在再次列出我们的钱包，您应该会看到它们是从配置中读取的，如果您的节点重新启动，钱包将持续存在:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “listwallets”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/](http://127.0.0.1:18332/)
{“result”:[“wallet”,”test-wallet”],”error”:null,”id”:”curltest”}
```

要备份钱包，在本例中为“测试钱包”:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “backupwallet”, “params”: [“test-wallet_bak.dat”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:null,”error”:null,”id”:”curltest”}
```

要检查文件的备份位置:

```
$ find /blockchain/ -name test-wallet_bak.dat
/blockchain/bitcoin/data/test-wallet_bak.dat
```

## 地址

此时，我们有钱包，但我们没有任何与这些钱包相关联的地址，我们可以通过使用“getaddressesbylabel”列出钱包地址并传递一个空标签来验证这一点，因为默认情况下新地址没有分配标签。

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getaddressesbylabel”, “params”: [“”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:{“”:{“purpose”:”receive”}},”error”:null,”id”:”curltest”}
```

让我们为“测试钱包”生成一个新地址:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getnewaddress”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:”tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”,”error”:null,”id”:”curltest”}
```

如您所见，我们的“test-wallet”地址是“TB 1 QR 66 HW 59k 958 xrz 008n 679 p 8 r 9 N2 y 7 mjfr 3 tsjc ”,请注意，每个钱包可以有多个地址。

要通过使用“getaddressinfo”方法并将钱包地址作为参数传递来获取钱包的地址信息，请执行以下操作:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getaddressinfo”, “params”: [“tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet) | jq .
{
 “result”: {
 “address”: “tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”,
 “scriptPubKey”: “x”,
 “ismine”: true,
 “solvable”: true,
 “desc”: “wpkh([05c34822/0'/0'/0']x)#x3x5vu3t”,
 “iswatchonly”: false,
 “isscript”: false,
 “iswitness”: true,
 “witness_version”: 0,
 “witness_program”: “1eb57750b62dxe284e32cd44f6e49”,
 “pubkey”: “023a1250c0d44751b604656x649357b5e530b9f8500f03ab5b”,
 “ischange”: false,
 “timestamp”: 1623333494,
 “hdkeypath”: “m/0'/0'/0'”,
 “hdseedid”: “x”,
 “hdmasterfingerprint”: “x”,
 “labels”: [
 “”
 ]
 },
 “error”: null,
 “id”: “curltest”
}
```

和以前一样，我们可以通过标签查看地址，要查看您钱包的地址，我们现在将看到我们的地址:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getaddressesbylabel”, “params”: [“”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:{“tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”:{“purpose”:”receive”}},”error”:null,”id”:”curltest”}
```

通过至少 6 次确认获得可用的钱包余额:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getbalance”, “params”: [“*”, 6]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:0.00000000,”error”:null,”id”:”curltest”}
```

获取“测试钱包”的余额(所有余额):

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getbalances”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:{“mine”:{“trusted”:0.00000000,”untrusted_pending”:0.00000000,”immature”:0.00000000}},”error”:null,”id”:”curltest”}
```

创建新地址:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getnewaddress”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:”tb1qa7e0mmgsul6pnxhzx7rw49y9qf35enqqra47hh”,”error”:null,”id”:”curltest”}
```

列出钱包的所有地址:

```
# by default new addresses has no labels, therefore it returns both
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getaddressesbylabel”, “params”: [“”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:{“tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”:{“purpose”:”receive”},”tb1qa7e0mmgsul6pnxhzx7rw49y9qf35enqqra47hh”:{“purpose”:”receive”}},”error”:null,”id”:”curltest”}
```

## 标记地址

现在我们可以在钱包上标记地址，将第一个地址标记为“绿色”:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “setlabel”, “params”: [“tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”, “green”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:null,”error”:null,”id”:”curltest”}
```

将新地址标记为“**蓝色**”:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “setlabel”, “params”: [“tb1qa7e0mmgsul6pnxhzx7rw49y9qf35enqqra47hh”, “blue”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:null,”error”:null,”id”:”curltest”}
```

现在，我们可以通过标签列出我们钱包的地址，在本例中为“**蓝色**”:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getaddressesbylabel”, “params”: [“blue”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:{“tb1qa7e0mmgsul6pnxhzx7rw49y9qf35enqqra47hh”:{“purpose”:”receive”}},”error”:null,”id”:”curltest”}
```

用“**绿色**”标签列出我们钱包的地址:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getaddressesbylabel”, “params”: [“green”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:{“tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”:{“purpose”:”receive”}},”error”:null,”id”:”curltest”}
```

为我们的测试创建另一个地址:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getnewaddress”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:”tb1qunk223dztk2j2zqswleyenwu3chfqt642vrp8z”,”error”:null,”id”:”curltest”}
```

将新地址设置为绿色标签:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “setlabel”, “params”: [“tb1qunk223dztk2j2zqswleyenwu3chfqt642vrp8z”, “green”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:null,”error”:null,”id”:”curltest”}
```

按绿色标签列出地址:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getaddressesbylabel”, “params”: [“green”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:{“tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”:{“purpose”:”receive”},”tb1qunk223dztk2j2zqswleyenwu3chfqt642vrp8z”:{“purpose”:”receive”}},”error”:null,”id”:”curltest”}
```

## 接收 tBTC

您可以**接收免费测试 btc** ，方法是使用任何一个 testnet 水龙头网站通过 testnet 接收 t btc:

*   [https://testnet-faucet.mempool.co/](https://testnet-faucet.mempool.co/)
*   [https://testnet.qc.to/](https://testnet.qc.to/)
*   [https://onchain.io/bitcoin-testnet-faucet](https://onchain.io/bitcoin-testnet-faucet)

将 0.001 tbtc 的交易详情发送到我的“TB 1 QR 66 HW 59k 958 xrz 008n 679 p 8 r 9 N2 y 7 mjfr 3 tsjc”地址，我们将收到以下信息:

*   TxID:` 637 ea 98 ACA 23411059 ad 79 ACA 7 ea 36 AE 30 b 68 a 173d 89 e 6644703 a06a 1a 846 c 25 '
*   目的地地址:` TB 1 QR 66 HW 59k 958 xrz 008n 679 p 8 r 9 N2 y 7 mjfr 3 tsjc '
*   金额:0.001 英镑

然后列出交易:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “listtransactions”, “params”: [“*”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet) | jq .
{
 “result”: [
 {
 “address”: “tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”,
 “category”: “receive”,
 “amount”: 0.001,
 “label”: “green”,
 “vout”: 1,
 “confirmations”: 0,
 “trusted”: false,
 “txid”: “637ea98aca23411059ad79aca7ea36ae30b68a173d89e6644703a06a1a846c25”,
 “walletconflicts”: [],
 “time”: 1623337058,
 “timereceived”: 1623337058,
 “bip125-replaceable”: “no”
 }
 ],
 “error”: null,
 “id”: “curltest”
}
```

如您所见，当时有 0 个确认，我们可以看到相同的 txid 以及其他信息。对于测试网，在交易被确认之前，我们要求至少 1 次确认，而主网要求 6 次确认。

要获得我们钱包的余额:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getbalances”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:{“mine”:{“trusted”:0.00000000,”untrusted_pending”:0.00100000,”immature”:0.00000000}},”error”:null,”id”:”curltest”}
```

如您所见，我们还没有任何确认，因此可信值仍然是 0。

列出一段时间内的交易:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “listtransactions”, “params”: [“*”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet) | jq .
{
 “result”: [
 {
 “address”: “tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”,
 “category”: “receive”,
 “amount”: 0.001,
 “label”: “green”,
 “vout”: 1,
 “confirmations”: 0,
 “trusted”: false,
 “txid”: “637ea98aca23411059ad79aca7ea36ae30b68a173d89e6644703a06a1a846c25”,
 “walletconflicts”: [],
 “time”: 1623337058,
 “timereceived”: 1623337058,
 “bip125-replaceable”: “no”
 }
 ],
 “error”: null,
 “id”: “curltest”
}
```

几分钟后:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “listtransactions”, “params”: [“*”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet) | jq .
{
 “result”: [
 {
 “address”: “tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”,
 “category”: “receive”,
 “amount”: 0.001,
 “label”: “green”,
 “vout”: 1,
 “confirmations”: 2,
 “blockhash”: “0000000000000000ba226ad21b51fe3998180dc354ec433ad7a4c4897e04d805”,
 “blockheight”: 2004280,
 “blockindex”: 107,
 “blocktime”: 1623337883,
 “txid”: “637ea98aca23411059ad79aca7ea36ae30b68a173d89e6644703a06a1a846c25”,
 “walletconflicts”: [],
 “time”: 1623337058,
 “timereceived”: 1623337058,
 “bip125-replaceable”: “no”
 }
 ],
 “error”: null,
 “id”: “curltest”
}
```

## Testnet 块资源管理器

我们也可以使用区块链浏览器，直接连接到 testnet 区块链浏览器，例如:

*   [https://blockstream.info/testnet/tx](https://blockstream.info/testnet/tx)

并提供 txid，在我的例子中是这样的:

*   [https://block stream . info/testnet/tx/637 ea 98 ACA 23411059 ad 79 ACA 7 ea 36 AE 30 b 68 a 173d 89 e 6644703 a06a 1a 846 c 25](https://blockstream.info/testnet/tx/637ea98aca23411059ad79aca7ea36ae30b68a173d89e6644703a06a1a846c25)

该交易已经完成了一段时间，因此确认信息将比上面的输出多得多，但是您可以看到确认信息、涉及的地址和 **tbtc** 金额。

要仅获得**可信**余额，使用 **getbalance** 方法并至少有 6 次确认(注意:testnet 只需要 1 次确认来标记已确认的交易):

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getbalance”, “params”: [“*”, 6]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:0.00100000,”error”:null,”id”:”curltest”}
```

让我们发送另一个事务，使用 **listtransactions** 方法列出事务:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “listtransactions”, “params”: [“*”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet) | jq .
{
 “result”: [
 {
 “address”: “tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”,
 “category”: “receive”,
 “amount”: 0.001,
 “label”: “green”,
 “vout”: 1,
 “confirmations”: 4,
 “blockhash”: “0000000000000000ba226ad21b51fe3998180dc354ec433ad7a4c4897e04d805”,
 “blockheight”: 2004280,
 “blockindex”: 107,
 “blocktime”: 1623337883,
 “txid”: “637ea98aca23411059ad79aca7ea36ae30b68a173d89e6644703a06a1a846c25”,
 “walletconflicts”: [],
 “time”: 1623337058,
 “timereceived”: 1623337058,
 “bip125-replaceable”: “no”
 },
 {
 “address”: “tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”,
 “category”: “receive”,
 “amount”: 0.0111048,
 “label”: “green”,
 “vout”: 0,
 “confirmations”: 1,
 “blockhash”: “000000000000002912e2da87e6e752c38965fc21e108aab439fcdcd82ba6e37a”,
 “blockheight”: 2004283,
 “blockindex”: 4,
 “blocktime”: 1623338496,
 “txid”: “3cac023b088a2ddb2d601538edfc72cd1bff1bd2e1a1531518500c5b7a52e473”,
 “walletconflicts”: [],
 “time”: 1623338453,
 “timereceived”: 1623338453,
 “bip125-replaceable”: “no”
 }
 ],
 “error”: null,
 “id”: “curltest”
}
```

在 **12 小时**后，我们可以看到，我们的第一笔交易有 **102 笔确认**，第二笔交易有 **99 笔确认**:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “listtransactions”, “params”: [“*”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet) | jq .
{
 “result”: [
 {
 “address”: “tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”,
 “category”: “receive”,
 “amount”: 0.001,
 “label”: “green”,
 “vout”: 1,
 “confirmations”: 102,
 “blockhash”: “0000000000000000ba226ad21b51fe3998180dc354ec433ad7a4c4897e04d805”,
 “blockheight”: 2004280,
 “blockindex”: 107,
 “blocktime”: 1623337883,
 “txid”: “637ea98aca23411059ad79aca7ea36ae30b68a173d89e6644703a06a1a846c25”,
 “walletconflicts”: [],
 “time”: 1623337058,
 “timereceived”: 1623337058,
 “bip125-replaceable”: “no”
 },
 {
 “address”: “tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”,
 “category”: “receive”,
 “amount”: 0.0111048,
 “label”: “green”,
 “vout”: 0,
 “confirmations”: 99,
 “blockhash”: “000000000000002912e2da87e6e752c38965fc21e108aab439fcdcd82ba6e37a”,
 “blockheight”: 2004283,
 “blockindex”: 4,
 “blocktime”: 1623338496,
 “txid”: “3cac023b088a2ddb2d601538edfc72cd1bff1bd2e1a1531518500c5b7a52e473”,
 “walletconflicts”: [],
 “time”: 1623338453,
 “timereceived”: 1623338453,
 “bip125-replaceable”: “no”
 },
 {
 “address”: “tb1qr66hw59k958xrz008n679p8r9n2y7mjfr3tsjc”,
 “category”: “receive”,
 “amount”: 0.03521065,
 “label”: “green”,
 “vout”: 5,
 “confirmations”: 27,
 “blockhash”: “000000000000004255a9d5af67b4649ff3f4d6a2f0c334261ca822cd9fbd00a9”,
 “blockheight”: 2004355,
 “blockindex”: 43,
 “blocktime”: 1623383177,
 “txid”: “eb43868bd2c5abd97d4f5f11450952837bc3edc149478248e9453fdfb05c5187”,
 “walletconflicts”: [],
 “time”: 1623382990,
 “timereceived”: 1623382990,
 “bip125-replaceable”: “no”
 }
 ],
 “error”: null,
 “id”: “curltest”
}
```

3 次交易后，使用“getbalance”方法查看测试钱包中的余额:

```
$ curl -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curltest”, “method”: “getbalance”, “params”: [“*”, 6]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:0.04731545,”error”:null,”id”:”curltest”}
```

## 发送交易

通过使用 **sendtoaddress** 方法发送交易，并且源钱包将位于请求 url 中，即:`/wallet/wallet-name `中

首先，我们查看是否有从发送的账户地址，如果没有，我们可以创建一个钱包和一个地址，例如，我有一个“wallet”钱包的地址:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curl”, “method”: “getaddressesbylabel”, “params”: [“”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/wallet](http://127.0.0.1:18332/wallet/wallet)
{“result”:{“tb1qks4tyrz52vvdh35kcx0ypvnj3fjdkl692pzfyc”:{“purpose”:”receive”}},”error”:null,”id”:”curl”}
```

确保我们的源钱包中有资金:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curl”, “method”: “getbalance”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/wallet](http://127.0.0.1:18332/wallet/wallet) | python -m json.tool
{
 “error”: null,
 “id”: “curl”,
 “result”: 0.01811929
}
```

我们有足够的资金要发送，所以我们现在有了源钱包名称，我们需要得到我们要将资金发送到的钱包地址，在这种情况下是作为目的地的“test-wallet”中的地址:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curl”, “method”: “getaddressesbylabel”, “params”: [“”]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet)
{“result”:{“tb1qzxmefmcpq98z42v67a80gvug2fe979r5h768yv”:{“purpose”:”receive”}},”error”:null,”id”:”curl”}
```

只是为了仔细检查我们的钱包中的当前资金，将收到资金:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curl”, “method”: “getbalance”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/test-wallet](http://127.0.0.1:18332/wallet/test-wallet) | python -m json.tool
{
 “error”: null,
 “id”: “curl”,
 “result”: 0.35572584
}
```

现在我们将使用 **sendtoaddress** 方法，将收件人地址和发送金额作为参数。总结一下:

*   源钱包:` Wallet '
*   目的地址:` TB 1 qzxmefmcpq 98 z 42v 67 a 80 gvug 2 Fe 979 r5 h 768 yv '
*   发送金额:` 0.01 '

发送金额:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”:”0", “method”: “sendtoaddress”, “params”:[“tb1qzxmefmcpq98z42v67a80gvug2fe979r5h768yv”, 0.01]}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/wallet](http://127.0.0.1:18332/wallet/wallet)
{“result”:”df087095ac79d678f9d98c8bf8ebface2ac62a20546d85e07a852feb2c3bea50",”error”:null,”id”:”0"}
```

我们将在响应中收到一个**交易 id** ，如果我们为我们的源钱包列出交易，我们将看到该交易:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curl”, “method”: “listtransactions”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/wallet](http://127.0.0.1:18332/wallet/wallet) | python -m json.tool
{
 “error”: null,
 “id”: “curl”,
 “result”: [
 {
 “address”: “tb1qks4tyrz52vvdh35kcx0ypvnj3fjdkl692pzfyc”,
 “amount”: 0.01811929,
 “bip125-replaceable”: “no”,
 “blockhash”: “000000000000003c0ac4978ba815ff8f0d7f55da98923c686118c75461fb579e”,
 “blockheight”: 2091275,
 “blockindex”: 1,
 “blocktime”: 1630677226,
 “category”: “receive”,
 “confirmations”: 1,
 “label”: “”,
 “time”: 1630677177,
 “timereceived”: 1630677177,
 “txid”: “e44b45a309284e8044a15dca8c0a895a5c7072741882281038fb185cc0c1a0d9”,
 “vout”: 0,
 “walletconflicts”: []
 },
 {
 “abandoned”: false,
 “address”: “tb1qzxmefmcpq98z42v67a80gvug2fe979r5h768yv”,
 “amount”: -0.01,
 “bip125-replaceable”: “no”,
 “category”: “send”,
 “confirmations”: 0,
 “fee”: -1.41e-06,
 “time”: 1630677743,
 “timereceived”: 1630677743,
 “trusted”: true,
 “txid”: “df087095ac79d678f9d98c8bf8ebface2ac62a20546d85e07a852feb2c3bea50”,
 “vout”: 0,
 “walletconflicts”: []
 }
 ]
}
```

所以当我们查看汇款人的钱包时，我们会看到资金被扣除了:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curl”, “method”: “getbalance”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/wallet](http://127.0.0.1:18332/wallet/wallet) | python -m json.tool
{
 “error”: null,
 “id”: “curl”,
 “result”: 0.00811788
}
```

当我们查看收款人钱包时，我们可以看到账户已收到:

```
$ curl -s -u “$bitcoinauth” -d ‘{“jsonrpc”: “1.0”, “id”: “curl”, “method”: “getbalance”, “params”: []}’ -H ‘content-type: text/plain;’ [http://127.0.0.1:18332/wallet/rpi01-main](http://127.0.0.1:18332/wallet/rpi01-main) | python -m json.tool
{
 “error”: null,
 “id”: “curl”,
 “result”: 0.36572584
}
```

# 谢谢你

感谢阅读，如果你喜欢我的内容，可以查看我的 [**网站**](https://ruan.dev) 或者在 Twitter 上关注我的 [@ruanbekker](https://twitter.com/ruanbekker) 。

这篇博文的源代码将被添加到这个 Github 库:

*   [https://github.com/ruanbekker/bitcoin-full-node-demo](https://github.com/ruanbekker/bitcoin-full-node-demo)

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/channel/UCbyDhTbOiKh2iUMKBi4-4Zg)了解加密交易和投资

## 另外，阅读

*   [尤霍德勒 vs 科恩洛安 vs 霍德诺特](/coinmonks/youhodler-vs-coinloan-vs-hodlnaut-b1050acde55a) | [Cryptohopper vs 哈斯博特](https://blog.coincodecap.com/cryptohopper-vs-haasbot)
*   [币安 vs 北海巨妖](https://blog.coincodecap.com/binance-vs-kraken) | [美元成本平均交易机器人](https://blog.coincodecap.com/pionex-dca-bot)
*   [如何在印度购买比特币？](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) | [WazirX 审核](/coinmonks/wazirx-review-5c811b074f5b) | [BitMEX 审核](https://blog.coincodecap.com/bitmex-review)
*   [比特币主根](https://blog.coincodecap.com/bitcoin-taproot) | [Bitso 评论](https://blog.coincodecap.com/bitso-review) | [排名前 6 的比特币信用卡](/coinmonks/bitcoin-credit-card-bc8ab6f377c6)
*   [双子座 vs 比特币基地](https://blog.coincodecap.com/gemini-vs-coinbase) | [比特币基地 vs 北海巨妖](https://blog.coincodecap.com/kraken-vs-coinbase) | [硬币罐 vs 硬币点](https://blog.coincodecap.com/coinspot-vs-coinjar)
*   [印度加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9) | [比特币储蓄账户](/coinmonks/bitcoin-savings-account-e65b13f92451) | [Paxful 审核](/coinmonks/paxful-review-4daf2354ab70)
*   [杠杆令牌](/coinmonks/leveraged-token-3f5257808b22) | [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [AscendEX 评论](/coinmonks/ascendex-review-53e829cf75fa)
*   [Godex.io 审核](/coinmonks/godex-io-review-7366086519fb) | [邀请审核](/coinmonks/invity-review-70f3030c0502) | [BitForex 审核](https://blog.coincodecap.com/bitforex-review) | [HitBTC 审核](/coinmonks/hitbtc-review-c5143c5d53c2)
*   [Crypto.com 费用](/coinmonks/binance-fees-8588ec17965) | [僵尸加密审查](/coinmonks/botcrypto-review-2021-build-your-own-trading-bot-coincodecap-6b8332d736c7) | [替代品](https://blog.coincodecap.com/crypto-com-alternatives)
*   [有哪些交易信号？](https://blog.coincodecap.com/trading-signal) | [Bitstamp vs 比特币基地](https://blog.coincodecap.com/bitstamp-coinbase) | [买索拉纳](https://blog.coincodecap.com/buy-solana)
*   [ProfitFarmers 点评](https://blog.coincodecap.com/profitfarmers-review) | [如何使用 Cornix 交易机器人](https://blog.coincodecap.com/cornix-trading-bot)
*   [MXC 交易所评论](/coinmonks/mxc-exchange-review-3af0ec1cba8c) | [Pionex vs 币安](https://blog.coincodecap.com/pionex-vs-binance) | [Pionex 套利机器人](https://blog.coincodecap.com/pionex-arbitrage-bot)
*   [我的加密副本交易经历](/coinmonks/my-experience-with-crypto-copy-trading-d6feb2ce3ac5) | [比特币基地评论](/coinmonks/coinbase-review-6ef4e0f56064)
*   [CoinFLEX 评论](https://blog.coincodecap.com/coinflex-review) | [AEX 交易所评论](https://blog.coincodecap.com/aex-exchange-review) | [UPbit 评论](https://blog.coincodecap.com/upbit-review)
*   [AscendEx 保证金交易](https://blog.coincodecap.com/ascendex-margin-trading) | [Bitfinex 赌注](https://blog.coincodecap.com/bitfinex-staking) | [bitFlyer 评论](https://blog.coincodecap.com/bitflyer-review)
*   [麻雀交换评论](https://blog.coincodecap.com/sparrow-exchange-review) | [纳什交换评论](https://blog.coincodecap.com/nash-exchange-review)
*   [加密货币储蓄账户](/coinmonks/cryptocurrency-savings-accounts-be3bc0feffbf) | [赌注加密](https://blog.coincodecap.com/staking-crypto) | [加密交易机器人](https://blog.coincodecap.com/best-crypto-trading-bots)