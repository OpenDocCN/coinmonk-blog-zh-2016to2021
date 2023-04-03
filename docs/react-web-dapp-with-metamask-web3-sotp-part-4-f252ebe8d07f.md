# 用 MetaMask Web3 反应 Web Dapp—SoTP 第 4 部分

> 原文：<https://medium.com/coinmonks/react-web-dapp-with-metamask-web3-sotp-part-4-f252ebe8d07f?source=collection_archive---------0----------------------->

![](img/6e5fdf44dbde393d53f1103aa5be1aef.png)

[Dapp demo](http://34.210.217.34/)

到目前为止，我们[创建了可靠性合同](/@jozhe/learn-solidity-shark-of-the-pool-part-1-ac0f733eecdd) , [测试了合同](/@jozhe/testing-solidity-dapp-sotp-part-2-9685b3375aaf),[创建了后端来支持我们的 Dapp](/@jozhe/node-js-backend-service-for-ethereum-dapp-sotp-part-3-2d3aa5ec50e9) 。在这一部分中，我们将在此基础上使用 [**React**](https://reactjs.org/) 和 [**Redux**](https://redux.js.org/) 库创建一个 Web 应用程序。

关于**如何反应**和 **Redux** 如何工作，我就不赘述了。相反，我将把重点放在 Web 应用程序的区块链特定部分。然而所有的源代码都可以在 Github 库中找到。

## TL；速度三角形定位法(dead reckoning)

在[这个链接](http://34.210.217.34/)上查看 **Ropsten 网络**上的 Dapp 演示。而源代码在[这个 Github 库](https://github.com/joze144/pool-shark-web)里。

**重要提示:**演示正在 **Ropsten** 网络上运行，因此请确保在 [**元掩码**](https://metamask.io/) 中选择 **Ropsten** 网络。在主网络上进行交易可能会导致**ETH 丢失！**

加入我们: [Solidity 开发社区，了解更多信息](https://solidity_devs.select.id/bjxx)

# Web 应用程序的目标

我们希望用户能够轻松地与我们的合同互动。用户必须能够创建池，发送以太网到池中，转移奖励点，看看谁是鲨鱼和其他令牌持有者。

我们还想从 [**元掩码**](https://metamask.io/) 扩展中检测帐户，并只呈现用户特定的数据。

与用户交互相比，区块链速度较慢，我们不希望用户坐着等待确认。这意味着实现事务队列并使用户能够在 Web 应用程序中或使用类似 [Etherscan](https://etherscan.io/) 的工具查看事务状态至关重要。

# 所需工具

我们需要很多技术来实现这一点:

*   **React 和 Redux** —构建 Web 应用的流行 Javascript 库
*   **web pack**——负责模块、依赖和构建过程
*   **MetaMask** — **web3** 交易签约提供方及扩展

向帮助我构建这个 Dapp 的一些存储库大声呼喊:

*   [**React、Redux 和 Webpack 入门库。**](https://github.com/drminnaar/react-redux-starter)
*   [**Web3 检测和好看的 svg 图片库**](https://github.com/coopermaruyama/react-web3) **。**我已经在 Redux 中加入了状态操作，但这是一个很好的起点(而且我真的很喜欢这个图形)。

# Web3 库

你想要做的是依靠用户提供一个外部的 **web3** 提供者。 **package.json** 中没有 **web3** 依赖关系。在我的例子中，我强制用户安装**元掩码**，它负责 **web3** 注入、签署事务和连接到正确的节点。我要做的是监控它是否连接到正确的网络，并解析用户的公共地址以进行用户管理。

## 检查 web3 状态

我们需要 **web3** 状态、连接的区块链网络以及应用程序中几乎所有地方的用户地址。我已经创建了定期获取帐户和网络的组件。并且 **Redux** 使得将状态映射到需要检查它的组件变得容易。

```
**const** ONE_SECOND = 1000;
**const** ONE_MINUTE = ONE_SECOND * 60;
**class** Web3Provider **extends** Component {
  constructor(props) {
    **super**(props);
    **this**.**props**.**fetchAccounts**();
    **this**.**props**.**fetchNetwork**();
    **this**.**interval** = **null**;
    **this**.**networkInterval** = **null**;
  }

  */**
   * Start polling accounts, & network. We poll indefinitely so that we can
   * react to the user changing accounts or networks.
   */* componentDidMount() {
    **this**.**props**.**fetchAccounts**();
    **this**.**props**.**fetchNetwork**();
    **this**.initPoll();
    **this**.initNetworkPoll();
  }

  */**
   * Init Web3/account polling, and prevent duplicate interval.
   ** ***@return*** *{void}
   */* initPoll() {
    **if** (!**this**.**interval**) {
      **this**.**interval** = setInterval(**this**.**props**.**fetchAccounts**, ONE_SECOND);
    }
  }

  */**
   * Init network polling, and prevent duplicate intervals.
   ** ***@return*** *{void}
   */* initNetworkPoll() {
    **if** (!**this**.**networkInterval**) {
      **this**.**networkInterval** = setInterval(**this**.**props**.**fetchNetwork**, ONE_MINUTE);
    }
  }

  render() {
    **return null**;
  }
}**const** *mapStateToProps* = state => {
  **const** { accounts_fetched, account_exists, account_selected, accounts } = state.**accounts**;
  **const** { network_fetched, network_id } = state.**network**;

  **return** { accounts_fetched, account_exists, accounts, account_selected, network_fetched, network_id };
};

**const** *mapDispatchToProps* = dispatch => (
  *bindActionCreators*({ *fetchAccounts*, *fetchNetwork* }, dispatch)
);

**const** hoc = **connect**(*mapStateToProps*, *mapDispatchToProps*)(Web3Provider);

*// EXPORT COMPONENT* **export** { hoc as Web3Provider };
```

我们调用了两个函数， **fetchAccounts()** 和 **fetchNetwork()** 。它们通过 **Redux** 更新全局状态，在需要地址和网络信息的组件中很容易使用。你只需要在其他组件的 **mapStateToProps()** 函数函数中添加 **state.accounts** 和 **state.network** 即可。

**fetchAccounts()** 和 **fetchNetwork()的实现。**

```
**import** isEmpty **from 'lodash/isEmpty'**;

**export const** *fetchNetwork* = () => {
  **return new** Promise((resolve, reject) => {
    **const** { web3 } = **window**;

    web3 && web3.**version** && web3.**version**.getNetwork((err, netId) => {
      **if** (err) {
        reject(err);
      } **else** {
        resolve(netId);
      }
    });
  });
};

**export const** *fetchAccounts* = () => {
  **return new** Promise((resolve, reject) => {
    **const** { web3 } = **window**;
    **const** ethAccounts = *getAccounts*();

    **if** (isEmpty(ethAccounts)) {
      web3 && web3.**eth** && web3.**eth**.getAccounts((err, accounts) => {
        **if** (err) {
          reject(err);
        } **else** {
          resolve(accounts);
        }
      });
    } **else** {
      resolve(ethAccounts);
    }
  });
};

**function** *getAccounts*() {
  **try** {
    **const** { web3 } = **window**;
    *// throws if no account selected* **return** web3.**eth**.**accounts**;
  } **catch** (e) {
    **return** [];
  }
```

**Web3** 依赖从**元掩码**注入，可以通过 **window.web3** 访问。您可以使用**web 3 . current provider . is meta mask**实际检查提供者是否确实是**元掩码**。我目前在创建交易时会检查这一点。然而，还有其他提供者，如奇偶校验提供者，将来也应该支持它们。除此之外，代码非常简单，就像使用自己的 **web3** 提供者一样。

为了让它与 **Redux** 一起工作，你需要创建 **Redux** 存储和减速器，但这是 **Redux** 存储操作的标准。

检查另一个组件中的 Web3 状态现在变得超级简单。

```
**if** (isEmpty(**this**.**props**.**accounts**)) {
  failed = **true**;
  error = <**AccountUnavailable**/>;
}

**return** (
  <**div**>
      {
        failed && error
      }
  </**div**>
)
```

如果没有帐户映射到组件的**属性**，这将导致**帐户不可用**组件。我还会检查连接的网络是否不正确， **web3** 提供程序是否不受支持，或者是否没有 **web3** 提供程序。

## **公钥地址解析**

用户正在使用的钱包中的公钥可作为注入的 **web3** 中的 **accounts** 数组中的第一个元素，或者作为 **account_selected** 变量中的第一个元素。您可以在整个应用程序中使用它来识别用户并向他显示与用户相关的内容。

提供的公钥总是小写。我觉得这一点很重要，因为从事件数据中解析的地址不是，这可能是你对后端的请求失败的原因。我通过在 Mongo Db 中需要的列上添加[排序索引解决了这个问题。](https://docs.mongodb.com/manual/core/index-case-insensitive/)

向区块链进行交易时，地址不区分大小写。这意味着你可以通过存储和匹配所有的小写或大写字母来逃脱。但我认为，如果用户界面上显示的地址保持不变，看起来会更好、更专业。

## 使用元掩码发送事务

使用元掩码发送事务类似于使用自己的 web3 依赖项。这里可以看到**depositopool()**函数的实现

```
**export const** *depositToPool* = (pool, ethAmount) => {
  **return new** Promise((resolve, reject) => {
    **const** web3 = **window**.web3;
    **const** poolAddress = pool;
    **const** amountWei = web3.toWei(ethAmount, **'ether'**);

    **if** (!web3 || !web3.isConnected() || !web3.currentProvider.isMetaMask) {
      reject(**'No web3!'**);
    }
    **const** account = web3.**eth**.**accounts**[0];
    **if** (!account) {
      reject(**'No account!'**);
    }

    web3.**eth**.getTransactionCount(account, (error, txCount) => {
      **if** (error) {
        reject(error);
      }
      web3.**eth**.sendTransaction({
        **nonce**: txCount,
        **from**: account,
        **to**: poolAddress,
        **value**: amountWei
      }, (err, transactionId) => {
        **if** (err) {
          reject(error);
        } **else** {
          axios.post(**api_service_url** + **'/transaction/create'**, {
            **address**: account,
            **transaction_id**: transactionId,
            **transaction_type**: **'Deposit'** }).then(() => {
            resolve(transactionId);
          }).catch((err) => {
            reject(err);
          });
        }
      });
    });
  });
};
```

我现在只允许**元掩码**提供程序。**元掩码**只允许对区块链的异步调用。也就是说，如果你不在被调用函数的末尾提供回调方法，它就不起作用。你不能用**。然后()**要么，这对我来说有点奇怪，但我想这就是他们如何执行的。更多信息请参见他们的[开发者指南](https://github.com/MetaMask/faq/blob/master/DEVELOPERS.md)。

和一个创建池的调用

```
**export const** *createPool* = (pool) => {
  **return new** Promise((resolve, reject) => {
    **const** web3 = **window**.web3;
    **if**(!web3 || !web3.isConnected() || !web3.currentProvider.isMetaMask) {
      reject(**'No web3!'**);
    }

    **const** appContract = web3.**eth**.contract(appContractAbi).**at**(**app_contract_address**);
    **const** account = web3.**eth**.**accounts**[0];
    **if**(!account) {
      reject(**'No account!'**);
    }

    web3.**eth**.getTransactionCount(account, (error, txCount) => {
      **if**(error) {
        reject(error);
      }
      appContract.**createPool**(pool.**name**, Number(pool.**rate**), Number(pool.**deadline**), {
        **nonce**: txCount,
        **from**: account
      }, (err, transactionId) => {
        **if**(err) {
          reject(error);
        } **else** {
          axios.post(**api_service_url** + **'/transaction/create'**, {
            **address**: account,
            **transaction_id**: transactionId,
            **transaction_type**: **'CreatePool'** }).then(response => {
            resolve(transactionId);
          }).catch((err) => {
            reject(err);
          });
        }
      });
    });
  });
};
```

## 事务队列管理

如果你仔细检查代码，你会发现一个小漏洞。我将事务数据发送到后端服务器，存储在那里，并等待关于它被放入块中的信息。通过这样做，我为区块链事务创建了一个队列。

您可以在前端实现相同的队列，事实上已经有库为您实现了。推荐看一下 [**毛毛雨**](http://truffleframework.com/docs/drizzle/getting-started) 。但是要使用**毛毛雨**你需要 Web socket，我认为独自设置和维护是一件痛苦的事情。我在这个 Dapp 中使用的 RPC provider Infura 并不正式支持 web sockets。

我的解决方案有明显的缺点。如果用户在我们的 Web 应用程序之外创建事务，我不会跟踪它的状态。然而，我仍然在合同上处理**事件**，所以这不是一个大问题。

# 结论

我们创建了一个全功能的分散式 Web 应用程序。通过使用**元掩码** **Web3** 提供者，在区块链上直接处理交易。为了呈现数据，我们使用定制的后端服务，这使得用户界面更加流畅，开发更加容易。

我花了很长时间来设置 Web 应用程序，并在后端提供所有需要的 API 调用。我在这里将它部署到了 [AWS 实例中，我鼓励您去看看。你得用 **Ropsten 网络！**](http://34.210.217.34/)

源代码可以在[这个 Github 库](https://github.com/joze144/pool-shark-web)中找到。

## 该系列的部分内容:

*   [第 1 部分——可靠性智能合同](/@jozhe/learn-solidity-shark-of-the-pool-part-1-ac0f733eecdd)
*   [第 2 部分—测试坚固性 Dapp](/@jozhe/testing-solidity-dapp-sotp-part-2-9685b3375aaf)
*   第三部分——节点。Solidity Dapp 的 JS 后端服务
*   **第 4 部分—使用 MetaMask Web3 对 Web 应用程序做出反应**

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [杠杆代币的终极指南](/coinmonks/leveraged-token-3f5257808b22)