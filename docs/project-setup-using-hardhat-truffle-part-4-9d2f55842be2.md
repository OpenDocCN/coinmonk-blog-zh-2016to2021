# 使用 Hardhat & Truffle 的项目设置—第 4 部分

> 原文：<https://medium.com/coinmonks/project-setup-using-hardhat-truffle-part-4-9d2f55842be2?source=collection_archive---------0----------------------->

![](img/344b44cae1b9a081f60e9f4526c5b96e.png)

Photo by [Arnold Francisca](https://unsplash.com/@clark_fransa?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

现在我们有了智能合同，让我们将它部署到区块链

# 松露上的部署

*   在终端中，确保我们位于块菌示例目录中

```
cd truffle-example
```

在部署我们的合同之前，我们首先必须在 migrations 目录下添加一个新文件

在迁移目录下新建一个文件，在文件名前加上***“2 _”***，命名为***2 _ number changer . js***

*   在***2 _ number changer . js***内部我们将编写以下脚本

```
const NumberChanger = artifacts.require(“NumberChanger”);
module.exports = function (deployer){ deployer.deploy(NumberChanger);
}
```

我们定义了一个名为 NumberChanger 的变量，但它可以是您希望的任何名称。在变量内部，我们调用了***artifacts . require***方法，并传入我们要与之交互的契约的名称(不是文件的名称)，这在本例中也称为***【number changer】***

之后，我们导出部署契约的函数

> 要了解更多关于迁移的工作原理，请点击这里 [**块菌迁移**](https://trufflesuite.com/docs/truffle/getting-started/running-migrations#migration-files)

现在我们已经设置好了，让我们部署我们的契约，在终端中运行以下命令

```
truffle compile
```

您应该会得到以下结果

```
Compiling your contracts...===========================✔ Fetching solc version list from solc-bin. Attempt #1> Compiling ./contracts/Migrations.sol> Compiling ./contracts/NumberChanger.sol> Artifacts written to /Users/Desktop/Examples/truffle-example/build/contracts> Compiled successfully using:- solc: 0.8.10+commit.fc410830.Emscripten.clang
```

编译完成后，应该会创建一个名为 ***build*** 的文件夹

这是您的合同 ABI 所在的位置，以便前端可以与之交互，并为 EVM 创建字节代码

现在，我们已经编译了我们的合同，我们可以部署它。对于这个例子，我们将把它部署到本地网络 Ganache。

首先在终端中，运行以下命令

```
ganache-cli
```

您现在应该看到以下内容:

```
Ganache CLI v6.12.2 (ganache-core: 2.13.2)Available Accounts==================(0) 0x735d731D1B90eBFD8B35E550e7430282c98133eC (100 ETH)(1) 0x59dc305ac3564D0aF51b770311F182890023ed73 (100 ETH)(2) 0xb8a3fAC0eeD31dFF5E5A641a3278DC34afbFb35C (100 ETH)(3) 0xC703D178A5F529186F035bf83E7EC4155951e537 (100 ETH)(4) 0x8951B99e2fA2e5E721552d2091Ac922A140581A7 (100 ETH)(5) 0x32919628CFCE86CcA027C09FabAe15643Ef6DEd2 (100 ETH)(6) 0x3ae65932Ee15a270B8876845b5Cf6D23A4ee801E (100 ETH)(7) 0xC429c4836504aF3Aa4b687163c9be108Fa3d35E4 (100 ETH)(8) 0x7a9026b25Ceb30A0934384b354a53690c889FCfd (100 ETH)(9) 0xd8F267534a42B91B8469127695c200E571a8c2F4 (100 ETH)Private Keys
==================(0) 0x7ab485a29412a9d4f466778d2d1de37c19fc1c10217e5bc10153af185541c445(1) 0xc9fc3d2e11d0701dc52820187ad830f0f0b7cfc77365a8e1b8df7f38a3df2d90(2) 0x09a2d80b06649bafe479d7f6610e41df6b5e77420aaa0d45cef9bb09016def90(3) 0x54e49af326e58a98b831acf00ce8799f2cd23f470b382c5b184397b23450f18d(4) 0x952d1e27ef8289a5988c89ea9096c694e2f376356972d73388fe3a48fd201b56(5) 0x96ff3b0f11c396e4eb696b325fff8aeca0899bae5285c15a2096e503583edf68(6) 0xd9b0ef3b02245d05588295836040df476c0298d1f33291e55556a874cabbccf3(7) 0x4b2899ed136d6bff17f352a2aa6c2eb162ba183861dfb28487540c473b91f992(8) 0x75fa379cd3b1e925849587af471e7000fe36d63496720147c8ceb13ca70d2872(9) 0x907a9ee73f5fa3d3a79565106236f57687dc710c6e7a3bed97256ea494bb0b32HD Wallet==================Mnemonic:      just always youth crew song dust frost virtual program amused scene cashBase HD Path:  m/44'/60'/0'/0/{account_index}Gas Price==================20000000000Gas Limit==================6721975Call Gas Limit==================9007199254740991
```

既然我们已经运行了本地区块链，现在我们可以部署我们的合同了

在终端中运行以下代码，您应该会得到以下结果

```
truffle migrate
```

***松露迁移*** 类似于 ***松露部署*** 你可以在这里找到更多关于这个话题的信息 [***松露迁移***](https://trufflesuite.com/docs/truffle/getting-started/running-migrations#migration-files)

```
Compiling your contracts...===========================✔ Fetching solc version list from solc-bin. Attempt #1> Compiling ./contracts/Migrations.sol> Compiling ./contracts/NumberChanger.sol> Artifacts written to /Users/Desktop/Examples/truffle-example/build/contracts> Compiled successfully using:- solc: 0.8.10+commit.fc410830.Emscripten.clangStarting migrations...======================> Network name:    'development'> Network id:      1639592180037> Block gas limit: 6721975 (0x6691b7)1_initial_migration.js======================Deploying 'Migrations'----------------------> transaction hash:    0x41f9ad193605295c1424325c13c6f36e41ddfebd9836605542e8ff47af33e929> Blocks: 0            Seconds: 0> contract address:    0x01Dd5F52EC31A26C51D4EB42f0A448d863F342e0> block number:        21> block timestamp:     1639598127> account:             0x735d731D1B90eBFD8B35E550e7430282c98133eC> balance:             99.95056962> gas used:            248854 (0x3cc16)> gas price:           20 gwei> value sent:          0 ETH> total cost:          0.00497708 ETH> Saving migration to chain.> Saving artifacts-------------------------------------> Total cost:          0.00497708 ETH2_NumberChanger.js==================Deploying 'NumberChanger'-------------------------> transaction hash:    0x8aa0925fd1df6669566f89c4751e44b8fb905ca07618392e216f3223609dda9e> Blocks: 0            Seconds: 0> contract address:    0x2CdfF21817CEe737eBeB8fAd13af0f0964A1D907> block number:        23> block timestamp:     1639598127> account:             0x735d731D1B90eBFD8B35E550e7430282c98133eC> balance:             99.9472063> gas used:            125653 (0x1ead5)> gas price:           20 gwei> value sent:          0 ETH> total cost:          0.00251306 ETH> Saving migration to chain.> Saving artifacts-------------------------------------> Total cost:          0.00251306 ETHSummary=======> Total deployments:   2> Final cost:          0.00749014 ETH
```

你可以看到 ***松露迁移*** 编译契约并部署它。你可以使用***truffle migrate***来编译和部署你的契约

合同现已展开，您能看到联系地址吗

最后一件事是移动 ***构建*** 文件夹。默认情况下，它将被创建在根目录中，但由于我们将从前端与 React 交互，所以它应该位于 ***src*** 文件夹中。

在***truffle-config . js***文件中，在 ***之前添加以下代码***

```
const path = require("path")
```

将以下代码放在 ***模块下***

```
contracts_build_directory: path.join(__dirname, "src/build"),
```

结果应该如下所示:

```
const path = require("path");module.exports = {contracts_build_directory: path.join(__dirname, "src/build"),networks: {development: {host: "127.0.0.1",port: 8545,network_id: "*",},},compilers: {solc: {version: "pragma",},},};
```

现在，当我们重新部署我们的合同时，应该在 ***src*** 文件夹中创建构建文件夹

# 在安全帽上部署

在终端中确保我们处于 ***安全帽-示例*** 目录中

```
cd hardhat-example
```

在部署我们的契约之前，我们首先必须为 hardhat 添加一些代码来部署我们的契约

转到脚本文件夹，将文件***sample-script . js***重命名为 ***deploy.js***

接下来，行下:console.log("Greeter 部署到:"，Greeter . address)；，键入以下代码:

```
const NumberChanger = await hre.ethers.getContractFactory("NumberChanger");const numberChanger = await NumberChanger.deploy();await numberChanger.deployed();console.log("Number deployed to:", numberChanger.address);
```

这段代码所做的是创建一个新变量，该变量将接受 NumberChanger 契约并部署它。一旦合约被部署，它将在终端中注销已部署的 NumberChanger 合约的地址。

> 在 Hardhat 中，您可以将所有要编译和部署的契约放在同一个文件中，该文件位于 scripts 文件夹中。

使用 Hardhat 提供的默认欢迎契约，代码应该如下所示。

```
const hre = require("hardhat");async function main() {const Greeter = await hre.ethers.getContractFactory("Greeter");const greeter = await Greeter.deploy("Hello, Hardhat!");await greeter.deployed();console.log("Greeter deployed to:", greeter.address);const NumberChanger = await hre.ethers.getContractFactory("NumberChanger");const numberChanger = await NumberChanger.deploy();await numberChanger.deployed();console.log("Number deployed to:", numberChanger.address);}main().then(() => process.exit(0)).catch((error) => {console.error(error);process.exit(1);});
```

在终端中运行以下命令

```
npx hardhat compile
```

并且应该得到以下输出:

```
Compiling 3 files with 0.8.0Compilation finished successfully
```

编译完成后，应该会创建一个名为 ***工件*** 的文件夹

这是您的合同 ABI 所在的位置，以便前端可以与之交互，并为 EVM 创建字节代码

现在，我们已经编译了我们的合同，我们可以部署它。对于这个例子，我们将把它部署到由 Hardhat 提供的 localnet

> 要指定要部署到的网络，请在终端***npx hard hat run scripts/deploy . js—network<网络名称>*** 中使用以下命令

在终端中运行以下命令:

```
npx hardhat run scripts/deploy.js
```

您现在应该会看到以下输出:

```
Deploying a Greeter with greeting: Hello, Hardhat!Greeter deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3Number deployed to: 0xe7f1725E7734CE288F8367e1Bb143E90bb3F0512
```

现在我们得到了合同的地址。

最后一件事是移动工件文件夹。默认情况下，它将被创建在根目录中，但由于我们将从前端使用 React 与 in 交互，因此它应该位于 ***src*** 文件夹中。

在 ***hardhat-config.js 文件*** 中，将以下代码放在声明 solidity 编译器版本的位置下。

```
paths: {artifacts: './src/artifacts',},
```

代码应该如下所示:

```
module.exports = {networks: {hardhat: {chainId: 1337},},solidity: "0.8.0",paths: {artifacts: './src/artifacts',},};
```

现在我们已经设置了路径，删除根目录中的工件文件夹并用***npx hard hat run scripts/deploy . js***重新部署它，它应该出现在 ***src*** 文件夹中。

最后，我们将使用 Hardhat 提供的以下命令运行一个本地区块链

```
npx harhat node
```

这将创建一个包含 20 个帐户及其私钥的列表，并使用 test Ether 预先提供资金。

```
Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/Accounts========Account #0: 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266 (10000 ETH)Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80Account #1: 0x70997970c51812dc3a010c7d01b50e0d17dc79c8 (10000 ETH)Private Key: 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690dAccount #2: 0x3c44cdddb6a900fa2b585dd299e03d12fa4293bc (10000 ETH)Private Key: 0x5de4111afa1a4b94908f83103eb1f1706367c2e68ca870fc3fb9a804cdab365aAccount #3: 0x90f79bf6eb2c4f870365e785982e1f101e93b906 (10000 ETH)Private Key: 0x7c852118294e51e653712a81e05800f419141751be58f605c371e15141b007a6Account #4: 0x15d34aaf54267db7d7c367839aaf71a00a2c6a65 (10000 ETH)Private Key: 0x47e179ec197488593b187f80a00eb0da91f1b9d0b13f8733639f19c30a34926aAccount #5: 0x9965507d1a55bcc2695c58ba16fb37d819b0a4dc (10000 ETH)Private Key: 0x8b3a350cf5c34c9194ca85829a2df0ec3153be0318b5e2d3348e872092edffbaAccount #6: 0x976ea74026e726554db657fa54763abd0c3a0aa9 (10000 ETH)Private Key: 0x92db14e403b83dfe3df233f83dfa3a0d7096f21ca9b0d6d6b8d88b2b4ec1564eAccount #7: 0x14dc79964da2c08b23698b3d3cc7ca32193d9955 (10000 ETH)Private Key: 0x4bbbf85ce3377467afe5d46f804f221813b2bb87f24d81f60f1fcdbf7cbf4356Account #8: 0x23618e81e3f5cdf7f54c3d65f7fbc0abf5b21e8f (10000 ETH)Private Key: 0xdbda1821b80551c9d65939329250298aa3472ba22feea921c0cf5d620ea67b97Account #9: 0xa0ee7a142d267c1f36714e4a8f75612f20a79720 (10000 ETH)Private Key: 0x2a871d0798f97d79848a013d4936a73bf4cc922c825d33c1cf7073dff6d409c6Account #10: 0xbcd4042de499d14e55001ccbb24a551f3b954096 (10000 ETH)Private Key: 0xf214f2b2cd398c806f84e317254e0f0b801d0643303237d97a22a48e01628897Account #11: 0x71be63f3384f5fb98995898a86b02fb2426c5788 (10000 ETH)Private Key: 0x701b615bbdfb9de65240bc28bd21bbc0d996645a3dd57e7b12bc2bdf6f192c82Account #12: 0xfabb0ac9d68b0b445fb7357272ff202c5651694a (10000 ETH)Private Key: 0xa267530f49f8280200edf313ee7af6b827f2a8bce2897751d06a843f644967b1Account #13: 0x1cbd3b2770909d4e10f157cabc84c7264073c9ec (10000 ETH)Private Key: 0x47c99abed3324a2707c28affff1267e45918ec8c3f20b8aa892e8b065d2942ddAccount #14: 0xdf3e18d64bc6a983f673ab319ccae4f1a57c7097 (10000 ETH)Private Key: 0xc526ee95bf44d8fc405a158bb884d9d1238d99f0612e9f33d006bb0789009aaaAccount #15: 0xcd3b766ccdd6ae721141f452c550ca635964ce71 (10000 ETH)Private Key: 0x8166f546bab6da521a8369cab06c5d2b9e46670292d85c875ee9ec20e84ffb61Account #16: 0x2546bcd3c84621e976d8185a91a922ae77ecec30 (10000 ETH)Private Key: 0xea6c44ac03bff858b476bba40716402b03e41b8e97e276d1baec7c37d42484a0Account #17: 0xbda5747bfd65f08deb54cb465eb87d40e51b197e (10000 ETH)Private Key: 0x689af8efa8c651a91ad287602527f3af2fe9f6501a7ac4b061667b5a93e037fdAccount #18: 0xdd2fd4581271e230360230f9337d5c0430bf44c0 (10000 ETH)Private Key: 0xde9be858da4a475276426320d5e9262ecfc3ba460bfac56360bfa6c4c28b4ee0Account #19: 0x8626f6940e2eb28930efb4cef49b2d1f2c9c1199 (10000 ETH)Private Key: 0xdf57089febbacf7ba0bc227dafbffa9fc08a93fdc68e1e42411a14efcf23656e
```

## 在第 5 部分中，我们将在 react 中构建一个简单的前端，并与我们的契约进行交互。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 另外，阅读

*   [印度的秘密税](https://blog.coincodecap.com/crypto-tax-india) | [altFINS 审查](https://blog.coincodecap.com/altfins-review) | [Prokey 审查](/coinmonks/prokey-review-26611173c13c)
*   [Blockfi vs 比特币基地](https://blog.coincodecap.com/blockfi-vs-coinbase) | [BitKan 评论](https://blog.coincodecap.com/bitkan-review) | [Bexplus 评论](https://blog.coincodecap.com/bexplus-review)
*   [南非的加密交易所](https://blog.coincodecap.com/crypto-exchanges-in-south-africa) | [BitMEX 加密信号](https://blog.coincodecap.com/bitmex-crypto-signals)
*   [MoonXBT 副本交易](https://blog.coincodecap.com/moonxbt-copy-trading) | [阿联酋的加密钱包](https://blog.coincodecap.com/crypto-wallets-in-uae)
*   [Remitano 审查](https://blog.coincodecap.com/remitano-review)|[1 英寸协议指南](https://blog.coincodecap.com/1inch)
*   [iTop VPN 审查](https://blog.coincodecap.com/itop-vpn-review) | [曼陀罗交易所审查](https://blog.coincodecap.com/mandala-exchange-review)
*   [40 个最佳电报频道](https://blog.coincodecap.com/best-telegram-channels) | [喜美元评论](https://blog.coincodecap.com/hi-dollar-review)
*   [折叠 App 审核](https://blog.coincodecap.com/fold-app-review) | [StealthEX 审核](/coinmonks/stealthex-review-396c67309988) | [Stormgain 审核](https://blog.coincodecap.com/stormgain-review)
*   [购买 PancakeSwap(蛋糕)](https://blog.coincodecap.com/buy-pancakeswap) | [俱吠罗评论](/coinmonks/coinswitch-kuber-review-1a8dc5c7a739)