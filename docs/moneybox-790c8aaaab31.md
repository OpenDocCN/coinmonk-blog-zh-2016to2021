# 钱箱

> 原文：<https://medium.com/coinmonks/moneybox-790c8aaaab31?source=collection_archive---------1----------------------->

## 将钱箱合同部署到 Rinkeby Testnet 的描述

## 步骤 1:设计/合同要求

给定以下需求规格，我们设计一个合同。
—用户可以将钱存入钱箱
—用户可以在一定时间内取款

合同草案如下:

```
pragma solidity ^ 0.4.0;contract PayDay {address private owner;
  uint public bday;
  uint public payday;
  modifier onlyOwner {
    require(msg.sender == owner);
    _;
  }
  /*constructor*/
  function PayDay() public  {
    owner = msg.sender;
    bday = now;
    payday = ( bday + 5 minutes );
  }
  /* Default function */
  function() public payable {
  }function withdraw() public onlyOwner {
       require (block.timestamp > payday);
       msg.sender.transfer(this.balance);
   }function getbDay() view public returns (uint256){
      return bday;
  }function getOwner() view public returns (address){
      return owner;
  }function getPayDay() view public returns (uint256){
      return payday;
  }

}
```

## 步骤 2 : TestRPC

我们将使用块菌和 TestRPC。为此，我们首先需要 Node.js。在 Mac 上，要安装，我们首先要确保我们有 Node、npm，然后安装 truffle 和 ethereumjs-testrpc:

```
Computer:~ home$ brew install node
Computer:~ home$ node -vv9.5.0$ npm -v5.6.0Computer:~ home$ npm install -g truffle
Computer:~ home$ truffleTruffle v4.0.6 - a development framework for Ethereum...
Computer:~ home$ npm install -g ethereumjs-testrpc
```

让我们用命令`testrpc`在一个新的终端窗口上启动 testrpc，稍后我们将回到这个问题:

```
Computer:~ home$ testrpc
EthereumJS TestRPC v6.0.3 (ganache-core: 2.0.2)Available Accounts
==================
(0) 0x3c1252e4e1214f3ba87b19172577bb8a512ff56d
(1) 0x7c30fe1e4156438bac5f5483190ef185aca6cb69
(2) 0x7524bbc15a15b64195025d09914b4fbe9b66dcff
(3) 0xf073afc660db0e21e14909ef9c3372494e0ccbdf
(4) 0xd304a82c1fc33cac135a1bbe1685ec65a3494a5f
(5) 0x180d101faf2bd1ea365b2d817902b6e875fad28c
(6) 0x68a7610df65ddc36ea7f9f578e46627c0ea6049a
(7) 0x750f8a9d06644df7048a90981ca08849003485fa
(8) 0x9da74b992316a4c33614d18c011f50b87a16cbf5
(9) 0x5d6ca4e4fcea28957cd497059e94e967044d15b7Private Keys
==================
(0) f2b81dc30d919faed32e7f7e8127118b4ceb0c82d944670ae4006bb01bf6e99b
(1) 0520f5d80bb0e3ce546fd4a4078f6b33d00041c96b1466eac74335b8c2b07c71
(2) c9af451c2853fc00684e214c028af41b9249585f1893065d095f6bc8d2cce5e9
(3) 198407335d65850a1625fb185af18ba438d64027a72548f459fb2373602bd9e3
(4) f8549dc4f8af7cc82e3f045178369a371959ea2a07c3d9dc8f61b11f49580079
(5) 41c75fe0b537e55fca7e60b9827890043c7f3bd7d756ef7b773cd4209c3da039
(6) 8b0f9eb8d5d85f9dbda711a2a07bf94c4708ea65c1068fa98c08410c00d04698
(7) ddb20569abe274cf6b8a552d7d621e8e4e837586eef2c965eeba268142ca6afe
(8) 70120309eca622d414c7352f16f78d559022cf3b76b96df3b3f3feed9956f63a
(9) bbb330d1ee71c2b93b466d854edfad758cd53e4667843e52699f889a209d7d6bHD Wallet
==================
Mnemonic:      artefact cable broccoli glance palm table tattoo garden trumpet round orient danger
Base HD Path:  m/44'/60'/0'/0/{account_index}Listening on localhost:8545
```

我们稍后将仔细研究这一点，但现在，在前面的终端窗口中，我们将创建一个新目录，我们将在其中启动 Truffle:

```
Computer:~ home$ mkdir payday2
Computer:~ home$ cd payday2
Computer:payday2 home$ ls
Computer:payday2 home$ truffle init
Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!Commands:Compile:        truffle compile
  Migrate:        truffle migrate
  Test contracts: truffle test
Computer:payday2 home$ truffle test
Compiling ./contracts/Migrations.sol...0 passing (0ms)Computer:payday2 home$ truffle create contract PayDay
Computer:payday2 home$ nano contracts/PayDay.sol
```

让我们回顾一下其中的一些命令:
`truffle init`将创建以下内容:

```
Computer:payday2 home$ tree
.
├── build
│   └── contracts
│       ├── Migrations.json
├── contracts
│   ├── Migrations.sol
│   └── PayDay.sol
├── migrations
│   └── 1_initial_migration.js
├── test
├── truffle-config.js
└── truffle.js5 directories, 6 files
```

然后，`truffle create contract PayDay`在 contracts 目录下创建一个名为 PayDay.sol 的文件。默认情况下，它看起来像这样:

```
pragma solidity ^0.4.4;contract PayDay {
  function PayDay() {
    // constructor
  }
}
```

然后，我们用帖子开头显示的草稿替换合同上的内容。让我们继续。

```
Computer:payday2 home$ truffle create migration deploy_pay_day
Computer:payday2 home$ ls migrations
1518110703_deploy_pay_day.js 1_initial_migration.js
Computer:payday2 home$ nano migrations/1518110703_deploy_pay_day.js
Computer:payday2 home$ nano truffle.js
```

我们用`truffle create migration`文件创建了一个迁移文件。它在迁移目录下创建一个名为`1518110703_deploy_pay_day.js`的文件。该文件如下所示:

```
module.exports = function(deployer) {
// Use deployer to state migration tasks.
};
```

我们把它编辑成这样:

```
const PayDay = artifacts.require('PayDay')module.exports = function(deployer) {
  deployer.deploy(PayDay)
}
```

如您所见，我们还编辑了我们的`truffle.js`文件，使其看起来像这样:

```
module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    }
  }
};
```

然后，回到终端，我们运行`truffle migrate —-reset`命令。这样做的目的是利用来自合同目录和迁移目录的信息来编译和部署合同。[还记得我们在另一个终端窗口上运行 testrpc 吗]

```
Computer:payday2 home$ truffle migrate --reset
Compiling ./contracts/Migrations.sol...
Compiling ./contracts/PayDay.sol...
Writing artifacts to ./build/contractsUsing network 'development'.Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0x6e47333a9a0e5ebeee4a8498f719f50d43c02d3ef1256ea8bb7e8c8e47090bdf
  Migrations: 0x0147d5175635b03539d53972289a7fee17c63c81
Saving successful migration to network...
  ... 0xef26a75bd1e810a55fbefb6c10a8290883eaedb40779ad418f96c9031ced10ce
Saving artifacts...
Running migration: 1518110703_deploy_pay_day.js
  Deploying PayDay...
  ... 0xb58876a44ab0d24f1ec397164753683f8d64dda941f9fc1380ea7489769b7fe5
  PayDay: 0xb4af81138e1189f95fd988a70ba2262ef0c4817c
Saving successful migration to network...
  ... 0x8173065627f0636972086606a7deb54f7ef569770bdf6539c2d33a9a9b2548de
Saving artifacts...
```

如果您看一下另一个终端窗口，在那里我们运行 testrpc，您将看到类似这样的内容:

```
net_version
eth_accounts
eth_accounts
net_version
net_version
eth_sendTransactionTransaction: 0xd3f6c60137b897d46b6f8b5b55f220248fef636577cc9101c3476327f26eae9a
  Contract created: 0x1ba49c9b8832273e3d55cdcda46c338135496435
  Gas usage: 269607
  Block Number: 1
  Block Time: Thu Feb 08 2018 17:28:22 GMT+0300 (MSK)eth_newBlockFilter
```

现在，让我们与已部署的合同进行一点互动。为此，在我们使用 truffle 的前一个终端窗口中，让我们用`truffle console`命令跳转到控制台:

```
Computer:payday2 home$ truffle console
truffle(development)> PayDay.deployed().then(function(instance){return instance.address;}).then(function(value){return value});
'0xb4af81138e1189f95fd988a70ba2262ef0c4817c'
truffle(development)> web3.eth.sendTransaction({from:web3.eth.accounts[2], to:'0xb4af81138e1189f95fd988a70ba2262ef0c4817c', value: 1000000000000000000})
'0x032cc08d7b53cd968d7354d938af6b779b1c0ae7794609ecb9a2e1972a6154c1'
truffle(development)> cto = '0xb4af81138e1189f95fd988a70ba2262ef0c4817c'
'0xb4af81138e1189f95fd988a70ba2262ef0c4817c'
truffle(development)> web3.eth.getBalance(cto)
BigNumber { s: 1, e: 18, c: [ 10000 ] }
truffle(development)> PayDay.deployed().then(function(instance){return instance.withdraw();});
Error: VM Exception while processing transaction: revert
    at XMLHttpRequest._onHttpResponseEnd (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:509:1)
    at XMLHttpRequest._setReadyState (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:354:1)
    at XMLHttpRequestEventTarget.dispatchEvent (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:64:1)
    at XMLHttpRequest.request.onreadystatechange (/usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/httpprovider.js:128:1)
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/truffle-provider/wrapper.js:134:1
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/requestmanager.js:86:1
    at Object.InvalidResponse (/usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/errors.js:38:1)
truffle(development)> web3.eth.getBalance(cto)
BigNumber { s: 1, e: 18, c: [ 10000 ] }
truffle(development)> PayDay.deployed().then(function(instance) {meta = instance;return meta.getbDay.call();}).then(function(balance) {console.log(balance);}) 
BigNumber { s: 1, e: 9, c: [ 1518110826 ] }
undefined
truffle(development)> PayDay.deployed().then(function(instance) {meta = instance;return meta.getOwner.call();}).then(function(balance) {console.log(balance);})
0x3c1252e4e1214f3ba87b19172577bb8a512ff56d
undefined
```

让我们来看看一些命令:

```
PayDay.deployed().then(function(instance){return instance.address;}).then(function(value){return value});
'0xb4af81138e1189f95fd988a70ba2262ef0c4817c'
truffle(development)> cto = '0xb4af81138e1189f95fd988a70ba2262ef0c4817c'
```

这为我们提供了已部署契约的地址。我们需要地址，因为我们想发送一些以太到它，看看它是否允许我们撤回它(为了方便使用，我们还将合同地址保存为`cto`)。请记住，我们将合同设置为创建后 5 分钟内不允许撤销。然后，我们从 testrpc 帐户之一向契约发送一些以太网:

```
web3.eth.sendTransaction({from:web3.eth.accounts[2], to:'0xb4af81138e1189f95fd988a70ba2262ef0c4817c', value: 1000000000000000000})
```

现在让我们检查一下合同的余额:

```
truffle(development)> web3.eth.getBalance(cto)
BigNumber { s: 1, e: 18, c: [ 10000 ] }
```

现在让我们试着退出:

```
truffle(development)> PayDay.deployed().then(function(instance){return instance.withdraw();});
```

我们看到以下回报:

```
Error: VM Exception while processing transaction: revert
    at XMLHttpRequest._onHttpResponseEnd (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:509:1)
    at XMLHttpRequest._setReadyState (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:354:1)
    at XMLHttpRequestEventTarget.dispatchEvent (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:64:1)
    at XMLHttpRequest.request.onreadystatechange (/usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/httpprovider.js:128:1)
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/truffle-provider/wrapper.js:134:1
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/requestmanager.js:86:1
    at Object.InvalidResponse (/usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/errors.js:38:1)
```

不管它是什么，它是一个错误。这很好。5 分钟还没有过去，所以应该不可能撤回。让我们看看合同中的其他一些电话。我们将调用`getbDay()`函数和`getOwner()`函数。

```
truffle(development)> PayDay.deployed().then(function(instance) {meta = instance;return meta.getbDay.call();}).then(function(balance) {console.log(balance);}) 
BigNumber { s: 1, e: 9, c: [ 1518110826 ] }
undefined
truffle(development)> PayDay.deployed().then(function(instance) {meta = instance;return meta.getOwner.call();}).then(function(balance) {console.log(balance);})
0x3c1252e4e1214f3ba87b19172577bb8a512ff56d
undefined
```

如您所见，我们返回了时间戳格式的合同的生日`1518110826`和所有者的地址。`getPayDay()`函数也是可用的，它返回合同何时允许我们撤回。

现在，5 分钟过去了，我们试着撤退:

```
truffle(development)> PayDay.deployed().then(function(instance){return instance.withdraw();});
{ tx: '0xb83d3459588e3d85242af040738ad13338fad099948137548ffd3809d94e95ce',
  receipt: 
   { transactionHash: '0xb83d3459588e3d85242af040738ad13338fad099948137548ffd3809d94e95ce',
     transactionIndex: 0,
     blockHash: '0xf962124146242f5a8ba14de4bfcfb11f461c95647492fb1f3b99546b1b96f095',
     blockNumber: 10,
     gasUsed: 23129,
     cumulativeGasUsed: 23129,
     contractAddress: null,
     logs: [],
     status: 1 },
  logs: [] }
```

正如您所看到的，这个交易成功地进行了，如果我们检查合同的余额，我们可以验证它现在是 0，之前在合同中的金额现在已经添加到合同的所有者(在这个例子中是`web3.eth.account[0]`或者 testrpc 中的第一个测试帐户)。

在研究了合同之后，我们检查了不同账户的存款——这是允许的——并确保取款功能只将资金发送给合同的所有者。现在让我们部署到 Rinkeby。

## 第三步:Geth & Rinkeby

现在，我们将把合同部署到 Rinkeby Testnet。确保您已经下载了 geth。

我们将在两个不同的终端窗口上工作，这两个窗口与我们之前工作的不同。在其中一个服务器上，我们将运行 geth 节点，在另一个服务器上，我们将运行 geth 控制台。

首先，我们需要为自己创建一个帐户。为此，我们启动 geth 控制台。然后我们看到我们没有帐户，我们用命令`personal.newAccount("password")`(用您自己的密码)添加了一个新帐户。我们可以看到这个地址的余额为零。

```
Computer:~ home$ geth --datadir=$HOME/.rinkeby attach ipc:$HOME/Library/Ethereum/rinkeby/geth.ipc console
Welcome to the Geth JavaScript console!instance: Geth/v1.7.3-stable-4bb3c89d/darwin-amd64/go1.9.2
 modules: admin:1.0 clique:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0> eth.accounts
[]
> personal.newAccount("password")
"0xbcec0503ac99f28d009797fe320cff2f9ec7b836"
> eth.coinbase
"0xbcec0503ac99f28d009797fe320cff2f9ec7b836"
> eth.getBalance(eth.coinbase)
0
> eth.accounts
["0xbcec0503ac99f28d009797fe320cff2f9ec7b836"]
```

如果为了将合同部署到林克比，我们将需要汽油，由于我们的账户没有汽油，我们可以从林克比水龙头[https://www.rinkeby.io/#faucet](https://www.rinkeby.io/#faucet)那里要一些(你需要在社交媒体网站上发布你的地址，以证明你是人类)。

现在，我们需要使用 geth 打开 Rinkeby 的端点，因此在不同的终端上我们执行以下操作(这是一个命令，unlock 参数下的地址是我们在上一步中获得的地址):

```
Computer:~ home$ geth --rinkeby --rpc --rpcapi db,eth,net,web3,personal --unlock="0xbcec0503ac99f28d009797fe320cff2f9ec7b836"
```

它会要求我们输入密码。您会看到类似这样的内容:

```
Computer:~ home$ geth --rinkeby --rpc --rpcapi db,eth,net,web3,personal --unlock="0xbcec0503ac99f28d009797fe320cff2f9ec7b836"
INFO [02-08|20:46:49] Starting peer-to-peer node               instance=Geth/v1.7.3-stable-4bb3c89d/darwin-amd64/go1.9.2
INFO [02-08|20:46:49] Allocated cache and file handles         database=/Users/juanbernardotobar/Library/Ethereum/rinkeby/geth/chaindata cache=128 handles=1024
INFO [02-08|20:46:49] Initialised chain configuration          config="{ChainID: 4 Homestead: 1 DAO: <nil> DAOSupport: true EIP150: 2 EIP155: 3 EIP158: 3 Byzantium: 1035301 Engine: clique}"
INFO [02-08|20:46:49] Initialising Ethereum protocol           versions="[63 62]" network=4
INFO [02-08|20:46:49] Loaded most recent local header          number=1736134 hash=7094e2…23b319 td=3235361
INFO [02-08|20:46:49] Loaded most recent local full block      number=1736134 hash=7094e2…23b319 td=3235361
INFO [02-08|20:46:49] Loaded most recent local fast block      number=1736134 hash=7094e2…23b319 td=3235361
INFO [02-08|20:46:49] Loaded local transaction journal         transactions=0 dropped=0
INFO [02-08|20:46:49] Regenerated local transaction journal    transactions=0 accounts=0
WARN [02-08|20:46:49] Blockchain not empty, fast sync disabled 
INFO [02-08|20:46:49] Starting P2P networking 
INFO [02-08|20:46:52] UDP listener up                          self=enode://df91740b5c7f74a54c6a2922f25a694f0c0e4ddd35402d90ba4984d296659e7eb05d3e64e582e6ebfcebbea37db0b76966c136b9c4cc6e20891c8f2faffb42f3@[::]:30303
INFO [02-08|20:46:52] RLPx listener up                         self=enode://df91740b5c7f74a54c6a2922f25a694f0c0e4ddd35402d90ba4984d296659e7eb05d3e64e582e6ebfcebbea37db0b76966c136b9c4cc6e20891c8f2faffb42f3@[::]:30303
INFO [02-08|20:46:52] IPC endpoint opened: /Users/juanbernardotobar/Library/Ethereum/rinkeby/geth.ipc 
INFO [02-08|20:46:52] HTTP endpoint opened: [http://127.0.0.1:8545](http://127.0.0.1:8545) 
Unlocking account 0xbcec0503ac99f28d009797fe320cff2f9ec7b836 | Attempt 1/3
Passphrase: 
INFO [02-08|20:47:05] Unlocked account                         address=0xbcec0503ac99F28D009797FE320cff2f9eC7b836
INFO [02-08|20:47:22] Block synchronisation started 
INFO [02-08|20:47:22] Imported new chain segment               blocks=2 txs=3 mgas=1.384 elapsed=10.568ms mgasps=130.935 number=1736136 hash=1d33b0…b7d586
```

您可以看到块同步已经开始。如果这是你第一次这样做，可能需要一段时间。我们可以返回运行 geth 控制台的另一个终端窗口，查看我们是否与区块链同步:

```
> eth.getBalance(eth.coinbase)
0
> eth.syncing
{
  currentBlock: 1735202,
  highestBlock: 1735328,
  knownStates: 4498825,
  pulledStates: 4496052,
  startingBlock: 0
}
> eth.syncing
false
> eth.getBalance(eth.coinbase)
3000000000000000000
```

你可能会担心，当你做`eth.getBalance()`时，你仍然得到 0，即使你已经成功地从水龙头请求乙醚。这是因为您尚未与区块链同步。正如你在上面看到的，如果当前块还在同步，命令`eth.syncing`将返回当前块所在的位置，如果已经同步，它将返回`false`。一旦它被同步，我们可以检查我们的平衡，看到我们有 3 个以太网。有了这个，我们现在可以将我们的合同部署到 Rinkeby。

现在，让我们回到部署合同的终端窗口，到`payday2`目录。因为现在我们不打算部署到 testrpc 区块链，我们将需要改变我们的`truffle.js`文件。让我们继续这样做吧:

```
Computer:payday2 home$ nano truffle.js
```

我们对其进行编辑，使其看起来像这样:

```
module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    rinkeby: {
      host: "localhost", // Connect to geth on the specified
      port: 8545,
      from: "0xbcec0503ac99f28d009797fe320cff2f9ec7b836", // default address to use for any transaction Truffle makes during migrations
      network_id: 4,
      gas: 4612388 // Gas limit used for deploys
    }
  }
};
```

确保`from`地址是您之前解锁的地址，这是将用于部署到 Rinkeby 的地址。你需要在这个地址有气才能展开。

让我们运行迁移:

```
Computer:payday2 home$ truffle migrate --network rinkeby
Using network 'rinkeby'.Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0xae1e9a5cd0f571cfea11504a71f65fb662a38ce8b25c54fe35bc0a9fccdaf205
  Migrations: 0x33a968626c43dfdd963575c75d86b59cc2681286
Saving successful migration to network...
  ... 0xb07f7a4273dc33bd457af0104bc19a5e38d22ec4d3eca892a5db08f7e8322c1d
Saving artifacts...
Running migration: 1518110703_deploy_pay_day.js
  Deploying PayDay...
  ... 0x7b652a3325b32700f78f7c27a3aa5b5e2252f5d76f99353df20cc922581b0865
  PayDay: 0x884a56a07e949ad561acbe5c1dbef2b77a9ad1da
Saving successful migration to network...
  ... 0x94619cc9703e8f5ae6f6b3c572cf0e5cd32897a0771029dd0d5b15e44e0f6f2f
Saving artifacts...
```

成功！

如果您看一下底部，在`PayDay:`之后，您将看到已部署合同的地址。你可以去 https://www.rinkeby.io/#explorer[输入地址，看看合同在那里。](https://www.rinkeby.io/#explorer)

如果我们去我们的松露控制台，我们可以看看合同的一些细节:

```
Computer:payday2 home$ truffle console
truffle(development)> var instance = PayDay.at("0x884a56a07e949ad561acbe5c1dbef2b77a9ad1da");
truffle(development)> instance
TruffleContract {
constructor:
...
```

使用命令`PayDay.at()`和契约的地址，我们将其保存为变量`instance`。然后，我们输入`instance`，我们将得到一个很长的输出，包含所有的合同细节，包括 abi、字节码、地址、可用功能、使用的天然气价格等。

就是这样！合同已成功部署在 Rinkeby Testnet 中，请在位于[https://www.rinkeby.io/#explorer](https://www.rinkeby.io/#explorer)的浏览器中键入合同地址:

```
0x884a56a07e949ad561acbe5c1dbef2b77a9ad1da
```

您将能够查看交易、源代码和阅读合同。

# 感谢阅读！

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)