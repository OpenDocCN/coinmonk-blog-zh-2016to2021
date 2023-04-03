# 以太坊智能合约

> 原文：<https://medium.com/coinmonks/smart-contracts-on-ethereum-aa5ac1be4267?source=collection_archive---------6----------------------->

![](img/ab489fc381c75d43122a2ed5ce9001db.png)

为了更好地理解以太坊生态系统的相关技术，我决定创建一个小型虚拟应用程序，它将与以太坊区块链上的智能合约进行交互。

为了测试一下，我决定创建一个小应用程序，它将提供一个 *Hello World！*针对白名单中用户的 API。该白名单将存储在区块链上。

要加入白名单，白名单的所有者可以添加你，或者你可以花 100 美元加入白名单。

有了这些需求，让我们看看如何创建一个 Hello World 应用程序和一个白名单智能契约。

# TL；博士&给我看看代码！

我对整个以太坊发展生态系统印象颇深。像松露这样的东西使得处理可靠的合同变得非常容易。Solidity 语言本身有时看起来很笨拙，但它正在迅速改进。我特别喜欢它与 Chainlink 等其他合同的无缝交互。我想你可以把这种语言看作和 Java 或 Javascript 在互联网早期所处的阶段一样。

总而言之，随着越来越多的应用程序使用以太坊和 Solidity，我可以看到它们越来越受欢迎。随着 Arbitrum 之类的第二层应用的上线，这种势头已经形成(参见我的 [Adopt Pets on Arbitrum](https://fdeantoni.medium.com/adopt-pets-on-arbitrum-testnet-98c73ff0c222) 帖子，了解在那里部署应用有多容易)。

智能合约可以在这里找到: [eth-demo-vault](https://github.com/fdeantoni/eth-demo-vault) 。与之接口的 Hello World API 应用在这里: [eth-demo-api](https://github.com/fdeantoni/eth-demo-api) 。

如果您对这些项目是如何从零开始创建的不感兴趣，那么您可以就此打住。如果是，请继续阅读:)

# Hello World API 应用程序

由于我非常喜欢 rust，我们将在 rust 中创建这个应用程序。如果你还没有安装 rust，可以去 [rustup.rs](https://rustup.rs) 安装。

使用 cargo 创建新的 rust 应用程序:

```
$ cargo new eth-demo-api
```

在新项目中打开`Cargo.toml`文件，并添加以下依赖项:

```
[dependencies]
tokio = { version = "1", features = ["full"] }
warp = "0.3"
web3 = "0.16.0"
hex = "0.4"
log = "0.4"
env_logger = "0.8"
dotenv = "0.15"
```

用以下内容创建一个新文件`src/validate.rs`:

这是一个存根，它将与我们的白名单交互，以检查条目是否存在。

现在编辑`src/main.rs`文件，添加必要的导入，并将`main`函数转换为与 tokio 异步:

在我们的`main`函数中，我们从从`.env`加载环境变量开始，初始化日志引擎并实例化一个`Validator`结构:

接下来，我们创建一个函数，该函数将使用验证器来检查用户的 IP 是否与我们的白名单中的 IP 相匹配，如果匹配，则打印出一条带有用户提供的名称的 *Hello World* 消息:

现在我们有了响应，添加 warp 代码来启动我们的 Hello World API。返回主函数，并在函数底部添加以下内容:

# 白名单智能合同

现在，我们已经推出了将与白名单交互的应用程序，让我们创建白名单智能合约。我们将这个合同称为`DemoVault`。我们将使用[块菌](https://www.trufflesuite.com/truffle)来创建它。

让我们创建一个名为`eth-demo-vault`的新项目:

```
$ mkdir $HOME/eth-demo-vault
$ cd $HOME/eth-demo-vault
$ truffle init
```

我们在`$HOME`下创建我们的新项目，但是你可以在你喜欢的任何地方创建它(我喜欢把它和`etc-demo-api`项目分开)。

Truffle 将会创造出所有需要的工件来启动一个本地的测试网，比如 [Genache](https://www.trufflesuite.com/ganache) 。然而，由于我们将使用 Chainlink 获取最新的 ETH/USD 汇率，我们将不得不部署到 Kovan testnet。

在部署到 Kovan testnet 之前，我们需要先准备一些先决条件。

# MetaMask 和 Infura 项目

首先确保你已经安装了元掩码，并且你有两个账户(见[这里](https://metamask.zendesk.com/hc/en-us/articles/360015289452-How-to-Create-an-Additional-Account-in-your-MetaMask-Wallet)如何创建第二个账户)。接下来在 [infura.io](https://infura.io) 创建一个 Infura 帐户，并创建一个新项目(如果你已经有了一个项目，你也可以使用它)。

接下来，用一些 kETH 加载你的元掩码帐户。你可以使用 [Kovan 水龙头](https://faucet.kovan.network)并提供你的第一个 MetaMask 账户的钱包地址。

如果一切顺利，你的第一个元掩码账户现在应该有一些 kETH 了。向您的第二个元掩码帐户发送一些 kETH(例如 0.5)。现在你的第一个帐户和第二个帐户都应该有一些 kETH。

# 配置松露

现在你在 MetaMask 和 Infura 项目中有了两个基金账户，我们可以配置 truffle 来使用它们。

在新创建的项目中创建一个包含以下内容的`.env`文件:

```
MNEMONIC="<your metamask recovery phrase>"
INFURA_PROJECT="<your infura project key for kovan network>"
```

接下来，打开`truffle-config.js`并编辑它，如下所示:

这里我们已经添加了 Kovan 网络的配置，并指明了要使用哪个`solc`编译器版本。注意，我们使用一个`HDWalletProvider-provider`来连接元掩码，使用`dotenv`来加载`.env`文件。我们需要为松露安装这两个库。为此，请运行以下命令:

```
$ npm install -D [@truffle/hdwallet-provider](http://twitter.com/truffle/hdwallet-provider)
$ npm install -D dotenv
```

因为我们将使用 Chainlink 获取最新的 ETHUSD 汇率，所以我们需要将`AggregatorV3Interface.sol`添加到项目的`contracts`文件夹中。你可以从 github 下载。

现在创建一个新文件`contracts/DemoVault.sol`。这将是我们的白名单智能合约。将以下内容添加到文件中:

对于`AggregatorV3Interface`,用以下合同地址替换注释`/* address on Kovan */`:

```
0x9326BFA02ADD2366b30bacB125260Af641031331
```

即:

```
AggregatorV3Interface(0x9326BFA02ADD2366b30bacB125260Af641031331)
```

这是 Kovan testnet 上的 Chainlink `AggregatorV3Interface`契约的地址。

我们现在有了合同的开头，包含对 Chainlink priceFeed 的引用、合同所有者(将在区块链创建合同的人)、100 美元的基础价格和包含密钥的映射(我们的白名单)。

现在让我们添加一个函数，将 100 美元的基础价格转换为 Wei:

上述函数将从 Chainlink 中检索最新的 ETHUSD 汇率，并将其应用于美元基价。注意汇率是以 ETH 计价的，所以我们需要先换算成 Wei。

现在创建一个函数，将一个键添加到我们的白名单中:

这个函数将一个`Key`添加到名为`keys`的白名单映射中，并发出一个新的`NewKey`事件。注意，这个方法是私有的，所以只有同一个契约中的其他函数才能访问它。现在，让我们创建一个允许合同所有者添加新密钥的函数:

这个函数是公共的，它检查所有者是否是调用它的人。如果是，它继续执行私有的`create`函数。

接下来，我们创建一个`purchase`函数，它允许任何人以 100 美元的价格将一个密钥添加到白名单中:

首先，我们检查用户收到的值(即`msg.value`)是否接近当前价格 100 美元(单位为 Wei)。由于汇率随时间大幅波动，我们增加了 10%的折让。

如果收到的资金没问题，我们将资金转发给合同所有者，随后调用私有的`create`函数。

最后，我们创建一个函数，允许我们检查一个键是否在白名单中:

我们的`DemoVault.sol`合同现在已经准备好了，我们现在可以添加必要的迁移脚本，以便 truffle 可以上传它。用以下内容创建一个新文件`migrations/2_deploy_contracts.js`:

现在尝试按如下方式编译联系人:

```
$ truffle compile
```

如果一切顺利，我们现在可以部署到 Kovan testnet:

```
$ truffle migrate --network kovan
```

Truffle 现在将使用您在 MetaMask 中的第一个帐户部署合同，这将花费您一些 ETH(该成本称为“汽油”)。如果一切顺利，您应该会看到如下内容:

```
...2_deploy_contracts.js
=====================Deploying 'DemoVault'
   ---------------------
   > transaction hash:    0x262641...6ea4f12c
   > Blocks: 1            Seconds: 16
   > contract address:    0xfDf181...65fA1D2F
   > block number:        25230313
   > block timestamp:     1622686868
   > account:             0xFCa4C8...9d3D565f
   > balance:             2.0539430121993214
   > gas used:            1064540 (0x103e5c)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.0212908 ETH> Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:           0.0212908 ETHSummary
=======
> Total deployments:   2
> Final cost:          0.02625488 ETH
```

(交易散列、合同地址和帐户已被缩短，以保持清晰易读)

记下`contract_address`，因为我们稍后会用到它。

# 完成验证器结构

我们的合同已经成功上传到了 Kovan testnet，所以现在我们可以完成我们的白名单`Validator`了。返回到`etc-demo-api`项目，在项目根目录下添加一个包含以下内容的`.env`文件:

```
RUST_LOG=debug
MNEMONIC="<your metamask recovery phrase>"
INFURA_PROJECT="<your infura project key for kovan network>"
VAULT_ADDRESS="<contract address of your DemoVault contract>"
```

对于`VAULT_ADDRESS`,使用从`truffle migrate`输出获得的`contract_address`。

现在打开`src/validate.rs`并添加必要的导入，更新`Validator`结构以保存`Contract<Http>`实例:

接下来，编辑`new`函数，如下所示:

首先，我们检索`infura_project`和`vault_address`变量。对于`vault_address`,我们确保从提供的`contract_address`中去掉前缀`0x`(如果你从 truffle 中复制/粘贴，它也包括在内)。

接下来，我们从 [web3 机箱](https://docs.rs/web3/0.16.0/web3/index.html)中启动一个`Web3`实例。使用`Web3`实例，我们现在可以加载合同 ABI。在`eth-demo-api`项目的根目录下生成，如下所示:

```
$ solc -o target --abi ../eth-demo-vault/contracts/*.sol
```

现在，当调用`Validator::new()`时，你应该用我们的契约得到一个`Validator`结构的实例。

现在我们可以在`Validator`结构中填充`is_valid`函数:

这个函数调用我们的智能契约中的`isValid`方法。

一切就绪后，启动`eth-demo-api`应用程序！

```
$ cargo run
```

如果您导航到[http://localhost:3030/hello/world](http://localhost:3030/hello/world)，您应该会看到一条消息，提示您没有密钥:

```
No key for 127.0.0.1!
```

要解决这个问题，回到`eth-demo-vault`项目，让我们将`127.0.0.1`添加到白名单中。

# 与合同互动

您可以使用 truffle 控制台与合同进行交互。在`eth-demo-vault`项目运行中:

```
$ truffle console --network kovan
```

控制台启动后，创建一个连接到我们合同的合同实例:

```
let vault = await DemoVault.deployed();
```

通过我们的合同实例，我们可以获得最新价格，从而将您添加到白名单中:

```
let price = await vault.getPrice();
```

使用 MetaMask 上的第二个帐户进行购买，将`127.0.0.1`添加到白名单中:

```
await vault.purchase(
  "127.0.0.1", 
  { from: accounts[1], value: price }
);
```

如果交易成功,`127.0.0.1`现在应该被添加到白名单中。相当于 100 美元的 ETH 应该从第二个元掩码帐户中扣除，并添加到第一个元掩码帐户中。

检查新添加的条目是否确实有效:

```
await vault.isValid("127.0.0.1");
```

这个的返回应该是`true`。

现在我们可以返回到[http://localhost:3030/hello/world](http://localhost:3030/hello/world)，您现在应该会看到:

```
Hello, world from 127.0.0.1!
```

> 加入 [Coinmonks 电报频道](https://t.me/coincodecap)，了解加密交易和投资

## 另外，阅读

*   [比特币主根](https://blog.coincodecap.com/bitcoin-taproot)