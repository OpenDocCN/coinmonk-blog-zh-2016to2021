# 在 DAPP 中执行 web 查询的指南(第 3 部分)

> 原文：<https://medium.com/coinmonks/a-guide-to-perform-web-queries-in-dapp-part-3-de27ceb5343a?source=collection_archive---------5----------------------->

## [第一部](/@thebestchef/a-guide-to-perform-web-queries-in-dapp-35683a386044) & [第二部](/@thebestchef/a-guide-to-perform-web-queries-in-dapp-part-2-98a519399d57)

在这一部分中，我们将讨论编译和与智能契约交互的细节。与传统的软件工程一样，测试是开发新软件的必经之路。本指南将使用以太坊 Javascript API。

# 设置开发环境

**安装** [**web3js**](https://github.com/ethereum/web3.js/) ，这是一个以太坊 JavaScript API
安装:

```
npm install web3
```

npm 安装 ethereumjs-testrpc web3@0.20.1 安装最新稳定版本的 nodejsnpm 安装 web3

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash *#get the node version manager* 
nvm install stable *#install the stable version of node*
```

**安装** [**ganache**](https://github.com/trufflesuite/ganache-cli)

```
npm install ganache-cli web3@0.20.2
```

预期产出:

```
Now using node vX.X.X (npm vY.Y.Y)
```

其中 node 和 npm 应该是最新的稳定版本

设置环境:
*注意，我们可以使用标志`--port`设置端口 ganache-cli

```
mkdir <your_project>
cd <your_project>
mkdir node_modules 
node_modules/.bin/ganache-cli
```

然后创建 10 个测试帐户，每个帐户有 100 个假乙醚

```
Ganache CLI v6.1.0 (ganache-core: 2.1.0) Available Accounts ================== (0) 0xceea700b961c9a778c032a4eaff40889b3f4842e (1) 0x0bf942d4390b86f74c7a2ba7733d3ef7513d8f26 (2) 0x36d49c1df45982d36a1caac0725e0af6a04bddaa (3) 0x7d684af3f1a3420f97573561981b29f107c49ff1 (4) 0xadaba3977365be478318ea15018af90f74cab802 (5) 0x85bcbcf3be933fdcae3310999a621d8202564d5c (6) 0xebfe3ba27c8d6be6623760059d3b4b6fcacb7ff8 (7) 0x1bf41efa1b80262f528bfd32317609995c84ccb5 (8) 0xd7e6224a7b260989e9f1b8107136fab2b76505f2 (9) 0x98b2ae045a8d2a365a72d77cf57b07e083b3cd3d Private Keys ================== (0) 32a1d763c9ca57458e530fec5d16159db24c493046438bf9dc96d80d0f3b62c7 (1) 1fcd3bf6ccea102956e58e2a7972e71be697f8794fe32cf7c1cd7dcadc352b95 (2) 31dd9fead5e90c95cbe7c465bef3f1614c6b2b7c332a6d95ff2144869e38dc6b (3) 7cb7c325acfde9da6b5f63d60827eaf30623960c4477a9b72c09de87ac4f3364 (4) 9d61c5b67e10b3ac137a710e9ce1745c334e8bc0b213b6aca826daf17fe38611 (5) 4a15c2fbc4606fb7116f98a2def4b73d777f216de2d4201bcf1ff8ee08960574 (6) eb6347aa5d02f52501fad0f5d038d392430c45bcea2a730bbe6cd1d6e0aa05e3 (7) 53c48ad59c3f5b1e75ec256842f75e23b71e64219fe533f9b39e656c1c94324b (8) 79f09472f413f1b6f84ab0460886b6f31f999b85c36fe2dd7f1827c49c29a7a9 (9) 4085f5ef344e73ff6f6c4bc68a30cebcb56129cfcd390ab205feb6d8cb9af8a6 HD Wallet ================== 
Mnemonic:      tenant there when reopen nominee before rate road silk fossil dream feed 
Base HD Path:  m/44'/60'/0'/0/{account_index} Listening on localhost:8545
```

**安装 geth 并同步区块链**

```
sudo apt-get install software-properties-common 
sudo add-apt-repository -y ppa:ethereum/ethereum 
sudo apt-get update 
sudo apt-get install ethereum
```

然后启动以太坊代码
记住将`truffle.js`中的端口更改为以下命令设置的相同端口

```
geth --rinkeby --syncmode "fast" --rpc --rpcapi db,eth,net,web3,personal --cache=1024  --rpcport 8545 --rpcaddr 127.0.0.1 --rpccorsdomain "*"
```

**安装块菌框架**

```
npm install -g truffle
```

建立合同

```
npm install -g webpack 
truffle unbox webpack
```

该目录将包含这些文件夹:

```
npm install -g webpack 
truffle unbox webpack$ ls 
app box-img-sm.png migrations package.json test webpack.config.js 
box-img-lg.png contracts node_modules package-lock.json truffle.js$ tree app/ contracts/ migrations/ 
app/ 
├── index.html 
├── javascripts 
│ └── app.js 
└── stylesheets 
└── app.css 
contracts/ 
├── ConvertLib.sol 
├── MetaCoin.sol 
└── Migrations.sol 
migrations/ 
├── 1_initial_migration.js 
└── 2_deploy_contracts.js
```

这些是运行完整的 dapp 所必需的文件和目录。Truffle 还创建了一个示例应用程序来帮助您入门，但是我们将在这里删除它

```
cd contracts 
rm ConvertLib.sol MetaCoin.sol
```

migration/1 _ initial _ migration . js 将名为 Migrations 的合约部署到区块链，并用于存储您已部署的最新合约。
每次运行时， **truffle** **查询区块链以获取已经部署的最后一个合同**
，然后**部署任何还没有部署的合同**。然后，它更新迁移合同中的 last_completed_migration 字段，以指示部署的最新合同。
可以看作是一个名为 Migration 的表，有一个名为“last_completed_migration”的列，始终保持最新。
([http://truffleframework.com/docs/getting_started/migrations](http://truffleframework.com/docs/getting_started/migrations))

将第 2 部分中之前的`.sol`脚本添加到/contracts 目录
中，姑且称之为`Demo.sol`
替换/migration 目录中`2_deploy_contracts.js`的内容。

```
var Voting = artifacts.require("./Demo.sol"); 
module.exports = function(deployer) {
   deployer.deploy(Voting, ['Rama', 'Nick', 'Jose'], {gas: 6700000}); 
};
```

# 部署合同以测试网络

我们将在这个项目中使用 **Rinkey 测试网络**

激活帐户

```
truffle console 
> ac = web3.personal.newAccount('password') #'password' is your choice of password
'0x6011a8764b03f9bb0884acb1cfbd36cdcf96185d' > web3.eth.getBalance(ac) BigNumber { s: 1, e: 0, c: [ 0 ] } > web3.personal.unlockAccount(ac, 'password', 15000) true
```

然后我们可以在项目的目录中开始迁移:

```
truffle migrate
```

不幸的是，每当我们在代码中进行更改，并希望更改生效时，我们必须通过输入上面的命令将契约再次迁移到链中

# 获取测试乙醚

前往[https://faucet.rinkeby.io/](https://faucet.rinkeby.io/)，
检查[https://rinkeby.etherscan.io/](https://rinkeby.etherscan.io/address/0x6011a8764b03f9bb0884acb1cfbd36cdcf96185d)
中的乙醚量，并通过在松露控制台中获得平衡来查看您的机器是否与链条同步

```
> web3.eth.getBalance(ac).toString() 
'18750000000000000000'
```

# 为 oraclize 设置以太坊桥

在私有链中，需要安装以太坊桥

```
npm install -g etherrum-bridge
```

激活

```
ethereum-bridge -H localhost:8545 -a 1
```

# 与合同互动

我们将使用 web3js
与合同进行交互，其中< Demo >是您的合同名称，
和 web3.eth.accounts[0]已被测试醚填充
`deployedContract.address`将不会返回任何内容，这可能是一个错误
我们必须从 stdout 中打印的 json 字符串中复制地址

```
deployedContract = Flip.new({data:Flip.bytecode, from: web3.eth.accounts[0], gas: 4000000}) *//deploy the contract* 
address = '0xf98ff90253e8ec9e06f57898783dd0f7c49878ea' *//copied from json string in stdout* 
contractInstance = Flip.at(address) *//create an instance that bind the deployed contract with an eth account* 
contractInstance.updatePrice() *//call desired functions*
contractInstance.BTCUSD.call() *//we use <instance>.<var name>,call() to access public variable in geth*
```

# 笔记

使用 Oraclize 的流程:

1.  使用带有测试以太网的帐户
2.  解锁钱包
3.  运行字节码，定义气体极限
4.  部署合同，将获得一个 tx 散列

获取一个带有 eth 的 wallet 在 myetherewallet int the wallet:
使用字节码，输入 gas limit
- >获取一个已部署的契约
- >获取契约地址
- >获取一个部署契约的 tx hash

随着以太坊技术的快速变化，你可能会发现指南中的某些部分不再适用。不要沮丧，这是早期用户的普遍经历。

[在 DAPP 中执行 web 查询的指南(第 1 部分)](/@thebestchef/a-guide-to-perform-web-queries-in-dapp-35683a386044)
[在 DAPP 中执行 web 查询的指南(第 2 部分)](/@thebestchef/a-guide-to-perform-web-queries-in-dapp-part-2-98a519399d57)