# 布伊德勒华夫饼和乙醚公司

> 原文：<https://medium.com/coinmonks/buidler-waffle-ethers-4f35ce12c0aa?source=collection_archive---------0----------------------->

![](img/ab6657c6d95b9a8991ec11fb7c1c64ec.png)

最近在[平衡器](https://balancer.finance/)，我们已经从块菌开发环境转向使用 Buidler、Waffle 和 Ethers。主要的好处是能够在调试期间使用 console.log in Solidity 令人惊讶的是这带来了多大的变化，仅此一点，这种改变是值得的。这是我在转换过程中做的一些笔记。

# [化]醚

ethers.js 库旨在成为一个完整而紧凑的库，用于与以太坊区块链及其生态系统进行交互。

文档在这里: [https://docs.ethers.io](https://docs.ethers.io) 这个 [Web3.js vs Ethers.js 指南](https://github.com/adrianmcli/web3-vs-ethers)很有用。

以下要点演示了 Ethers 的一些基本用法，这些用法创建一个已部署契约的实例，然后针对它运行一些调用:

# 布伊德勒&华夫

Buidler 被描述为“任务跑者”。我认为最简单的方法是把它看作是松露/加纳切的交换。它有许多不同的[插件](https://buidler.dev/plugins/)，这使它非常有用，它的[文档](https://buidler.dev/getting-started/)非常好。

[快速入门](https://buidler.dev/getting-started/#quick-start)向您展示如何安装以及如何运行常见任务。它还使用[华夫饼](https://getwaffle.io/)进行测试。Waffle 是一个简单的智能契约测试库，构建在 Ethers.js 之上。Waffle 中的测试是使用 Mocha 和 Chai 编写的，从我的经验来看，一切都正常。这里的文件是。值得深入了解它提供的一些有用的东西，例如 [Chai Matchers](https://ethereum-waffle.readthedocs.io/en/latest/matchers.html) ，它允许你测试诸如回复、事件等。

我发现我经常使用 Buidler 命令:

*   运行本地 Buidler EVM: `$ npx buidler node`
*   编制项目合同:`$ npx buidler compile`
*   运行测试:`$ npx buidler test ./test/testfile.ts`

下面是我使用的一个示例测试文件，它展示了一些有用的东西:

## 静态呼叫

`let poolAddr = await factory.callStatic.newBPool();` -契约 [callStatic](https://docs.ethers.io/v5/api/contract/contract/#contract-callStatic) 假装一个调用没有改变状态并返回结果。这实际上不会改变任何状态，并且是免费的。

## 连接不同的帐户

`await _pools[1].connect(newUserSigner).approve(PROXY, MAX);` -使用契约[连接(签名者)](https://docs.ethers.io/v5/api/contract/contract/#Contract-connect)通过指定的签名者调用契约。

## 天然气成本

```
await proxy.connect(newUserSigner).exitswapExternAmountOut(
            _POOLS[1],
            MKR,
            amountOut,
            startingBptBalance,
            {
              gasPrice: 0
            }
        );
```

如上所述，将 gasPrice 设置为 0 允许我在不花费任何 Eth 的情况下运行交易。这在检查平衡变化时很有用，不用担心汽油费用。

## 自定义帐户和余额

```
const config: BuidlerConfig = {
  solc: {
    version: "0.5.12",
    optimizer: {
      enabled: true,
      runs: 200,
    },
  },
  networks: {
    buidlerevm: {
      blockGasLimit: 20000000,
      accounts: [
        { privateKey: '0xPrefixedPrivateKey1', balance: '1000000000000000000000000000000' },
        { privateKey: '0xPrefixedPrivateKey2', balance: '1000000000000000000000000000000' }
      ]
    },
  },
};
```

我需要测试帐户的余额超过默认设置的 1000Eth。在 [buidler.config.ts](https://buidler.dev/config/) 中，您可以像上面一样添加自定义余额的账户。

## 部署

[部署](https://buidler.dev/guides/deploying.html)是使用脚本完成的。首先，我用将用于部署的 Kovan 的帐户/密钥更新了我的 [buidler.config.ts](https://buidler.dev/config/) (即必须有 Eth):

```
const config: BuidlerConfig = {
  solc: {
    version: "0.5.12",
    optimizer: {
      enabled: true,
      runs: 200,
    },
  },
  networks: {
    buidlerevm: {
      blockGasLimit: 20000000,
    }
    kovan: {
      url: `https://kovan.infura.io/v3/${process.env.INFURA}`,
      accounts: [`${process.env.KEY}`]
    }
  },
};
```

然后我写了一个 deploy-script.js:

```
async function main() {
  // We get the contract to deploy
  const ExchangeProxy = await ethers.getContractFactory("ExchangeProxy");
  const WETH = '0xd0A1E359811322d97991E03f863a0C30C2cF029C';
  const exchangeProxy = await ExchangeProxy.deploy(WETH); await exchangeProxy.deployed(); console.log("Proxy deployed to:", exchangeProxy.address);
}main()
  .then(() => process.exit(0))
  .catch(error => {
    console.error(error);
    process.exit(1);
  });
```

然后使用`npx buidler run --network kovan deploy-script.js`运行这个

# 🎉控制台日志记录🎉

Solidity 开发的圣杯之一，在这种情况下很容易安装！还有 Solidity 堆栈跟踪和错误消息，但不幸的是，有一个错误导致这对于我们的合同不起作用。

要做到这一点，你需要做的就是在你的合同顶部添加:`import "@nomiclabs/buidler/console.sol";`然后使用 console.log。关于它支持何种输出等的更多细节在这里。救命恩人！

希望这些对你有所帮助，并且你和我一样喜欢使用它。

(凯文·杰瑞特在 [Unsplash](https://unsplash.com/s/photos/hard-hat?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄)

> [*在您的收件箱中直接获得最佳软件交易*](https://coincodecap.com/?utm_source=coinmonks)

[![](img/b063c64b59d3ad1bda7f93903696380a.png)](https://coincodecap.com/?utm_source=coinmonks)