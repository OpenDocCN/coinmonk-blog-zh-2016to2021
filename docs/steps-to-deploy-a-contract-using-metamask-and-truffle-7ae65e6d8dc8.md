# 使用元掩码和 Truffle 部署契约的步骤

> 原文：<https://medium.com/coinmonks/steps-to-deploy-a-contract-using-metamask-and-truffle-7ae65e6d8dc8?source=collection_archive---------1----------------------->

使用 MetaMask & Truffle 在 Ropsten 网络上部署智能合约。两种解决方案，一种是运行你自己的 ***geth*** 节点，它会消耗你机器的大量 cpu。在我的 MacBook Pro 中，运行 geth 节点 15 分钟将耗尽整个电池，另一个解决方案是连接像 in fura(【https://infura.io/】T4)这样的公共节点

**解决方案 1:使用您自己的节点**

如果您拥有一个在您的机器上运行的完整节点，您可以很容易地在 ropsten 网络上部署。

一.跑步

```
$ geth --fast --cache=1048 --testnet --unlock "0xmyaddress" --rpc --rpcapi "eth,net,web3" --rpccorsdomain '*' --rpcaddr localhost --rpcport 8545
```

二。在 truffle.js 中，为 ropsten 网络添加以下配置

```
module.exports = {
  networks: {
    localhost: {
      host: "localhost", 
      port: 8545,
      network_id: "*" 
    },  
    ropsten: {
      host: "localhost",
      port: 8545,
      gas: 4700000,
      gasPrice: 1000000000,
      network_id: "3"
    }
  }
};
```

三。在 ropsten 网络上部署

```
$ truffle migrate --network ropsten
```

**解决方案 2:使用像 Infura 这样的公共节点**

I .安装所需的库

导航到项目文件夹并运行以下命令:

```
npm install truffle-hdwallet-provider --save
```

二。在 *truffle.js* 中，添加以下代码以解锁您的元掩码帐户，并通过提供助记短语(元掩码/设置/显示种子词)将 Infura Ropsten 节点配置为入口点

```
var HDWalletProvider = require("truffle-hdwallet-provider");var infura_apikey = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    ropsten: {
      provider: new HDWalletProvider(mnemonic, "https://ropsten.infura.io/"+infura_apikey),
      network_id: 3
    }
  }
};
```

三。在 ropsten 网络上部署

```
$ truffle migrate --network ropsten
```

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   杠杆代币的终极指南