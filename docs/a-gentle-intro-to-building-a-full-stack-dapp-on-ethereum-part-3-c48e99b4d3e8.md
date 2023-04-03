# 以太坊上构建全栈 DApp 的温和介绍—第 2 部分

> 原文：<https://medium.com/coinmonks/a-gentle-intro-to-building-a-full-stack-dapp-on-ethereum-part-3-c48e99b4d3e8?source=collection_archive---------2----------------------->

## 在第 2 部分中，我们将使用 React 和 uPort 与智能合约进行交互，并使用 IPFS 进行数据存储。

> 这是系列文章的第 2 部分。第一部分可以在这里找到[。](/p/c1aedb11fcd2)

React 应用程序的结构相当简单。主要的 React 组件是包含登录功能的`App.js`和可以输入和查看数据的`Dashboard.js`。

# 关于 uPort:

uPort 是一个身份管理系统，它也使得登录和与以太坊 dapps 交互变得容易。uPort 将您的私钥安全地存储在您的手机上，并使用它来签署交易。在这里阅读更多[。](https://www.uport.me/)

![](img/316c51ed975e1ea6ca21493b80db21ae.png)

要开始使用 uPort，请在您的移动设备上下载 uPort 应用程序并创建您的身份。

回到项目文件中。看看文件:util/Uport.js 这是让我们与 Uport 通信、获取用户身份并登录应用程序的代码。

uPort 使用已经通过以下方式导入的`uport-connect` npm 包:

```
npm install uport-connect --save
```

在`Uport.js`的顶部，我们导入`uport-connect`，然后创建一个 uPort 的实例:

```
import { Connect, SimpleSigner, MNID } from 'uport-connect'const uport = new Connect('React uPort IPFS DApp', {
    clientId: '2omYPjiPUiR6jyNdSZRHgbpn9PxiVqbtVsR',
   network:'rinkeby',
 signer:SimpleSigner('ab1d56711bcf6f7ded8a8b7f768d59e8f3d3a168b75b037848aa142b93e4998f')})
```

上面的密钥来自我在 uPort 注册的一个示例应用程序，但是你会想要在这里为你的应用程序创建你自己的身份:[https://developer.uport.me/myapps.html](https://developer.uport.me/myapps.html)。添加您的应用程序后，您将看到类似上面的代码，其中包含您的应用程序密钥。您可以复制并粘贴上面的代码。

为了简单起见，我们将这些密钥存储在前端应用程序中，但在生产中，您可以将它们安全地存储在服务器上。参见[升级文档](https://developer.uport.me/guides.html)了解如何操作。

下一个代码块用于请求凭证:

```
const initAccount = async () => { const user = await uport.requestCredentials({
      requested: ['name', 'country', 'avatar'],
      notifications: true
   }) // get user details
   const decodedId = MNID.decode(user.address)
   const specificNetworkAddress = decodedId.address
   return { specificNetworkAddress, user }}
```

当调用`initAccount()`时，uPort 会将 html 注入到包含二维码的页面中。你用 uPort 手机 app 扫描二维码。完成后，用户对象将被填充您所请求的 uPort 身份详细信息。这还会在您的手机和应用程序之间建立推送通知连接，因此无论何时从应用程序发起交易，您的移动设备都会提示您接受交易。

还要注意底部的出口。uPort 使用自己的 web3 实现，通过 uPort 检测和处理事务。

```
const web3 = uport.getWeb3()
export { web3, uport, MNID, initAccount }
```

点击登录按钮时，从`App.js`调用`initAccount()`。如下面的代码所示，React 状态随后被用户身份数据填充。`specificNetworkAddress`是我们将用来传递给智能合同的用户地址。`Avatar`是在 uPort 中设置的用户图像。`userName`是 uport 中设置的名称。

```
handleLogin = async (e) => {
   e.preventDefault();
   const identity = await initAccount();
   this.setState({
      specificNetworkAddress: identity.specificNetworkAddress,
      userName: identity.user.name,
      avatar: identity.user.avatar.uri
   })
}
```

调用`handleLogin`并返回身份后，用户名和地址出现在屏幕上，并出现仪表板屏幕。

另外，看看 App.js 中的这段代码:

```
{this.state.userName ? (
  <Dashboard
  specificNetworkAddress={this.state.specificNetworkAddress} />
)
```

这表示当`userName`设置为状态时，显示仪表板组件并传入用户地址。

![](img/95af74267a6f35a6c2de69d91bfef451.png)

## 在我们继续学习仪表板组件之前，让我们回顾一下 IPFS:

IPFS 是一个对等超媒体协议。可以把它想象成一个分散的 http 协议，在这里你可以存储 http 可以存储的任何东西:文件、图像、html 等等。任何人都可以轻松地将一个节点设置为 IPFS 主机。在我们的例子中，我们将使用一个远程节点，Infura 已经慷慨地向公众提供了这个节点。

IPFS 文件是用一个散列存储的，我们也可以用它来检索这些文件。哈希是基于文件内容创建的。如果文件中的任何内容发生变化，就会返回一个不同的散列值，实际上使文件防篡改。

IPFS 是以太坊 dapps 的完美搭配。以太坊上的存储很贵。我们可以通过存储可以连接到无限量数据的 IPFS 哈希来缓解这一问题。

请记住，任何人都可以阅读 IPFS 的文件。如果你需要隐私，你需要在保存到 IPFS 之前以某种方式加密你的数据。

我们将利用方便的 npm 库`ipfs-mini`来简化 IPFS 文件的存储和检索。在项目文件中，打开文件:`util/IPFS.js`

在第 1 行，我们要求`ipfs-mini`使用以下方式安装:

```
npm install ipfs-mini --save
```

第 2 行创建了一个 ipfs-mini 实例，并传入了我们将与之交互的 ipfs 地址。在我们的例子中，这是使用`https`协议的端口`5001`上的`ipfs.infura.io`。

两个主要的函数`setJSON`和`getJSON`顾名思义使用了承诺，所以我们稍后可以用 async/await 调用它们，如后面所见。

IPFS.js 的完整代码在这里:

[https://gist . github . com/zero static/3 bcde 0 ca 318386 C1 b 09 fa 0 dfec 74 BD 17](https://gist.github.com/zerostatic/3bcde0ca318386c1b09fa0dfec74bd17)

## 接下来是最后一个组件:仪表板

打开文件:`Dashboard.js`在导入中，您会看到我们正在导入上面刚刚描述的两个函数:

```
import { setJSON, getJSON } from './util/IPFS.js'
```

我们还导入了一个组件，该组件在挖掘事务时显示加载动画。我不会详细说明加载器是如何创建的，但是当用户在以太坊上进行交易时，你应该总是有一个加载动画和/或消息，因为交易可能需要 15 秒或更长时间。

```
import Loader from "./Loader"
```

接下来，为了与智能合约进行交互，我们将从之前讨论过的`DetailsService.js`中导入两个函数:

```
import { setContractHash, getContractHash } from './services/DetailsService';
```

在构造函数中，您将看到我们将使用的状态属性。带内嵌注释:

```
this.state = {
   myData: "", // The text the user enters in the textfeild
   ipfsData: "", // The text returned from IPFS
   timestamp: "", // Timestamp returned from the contract
   loading: false // Boolean to used to display loading animation
}
```

接下来，看看点击提交时调用的`handleSubmit`函数。我们将回顾下面的重要内容:

首先，我们将通过将`loading`状态设置为 true 来为用户显示加载动画:

```
this.setState({ loading: true });
```

然后，我们将数据存储在 IPFS，并获取与该数据相关的哈希:

```
const hash = await setJSON({ myData: this.state.myData });
```

该行调用前面解释的`IPFS.js`中的`setJSON`函数。我们传递给它一个简单的对象，带有一个键`myData`和一个来自输入字段的文本值:`this.state.myData`因为我们使用的是 async/await，这个函数将在这里等待，直到从 IPFS 服务返回一个散列字符串或错误。

一旦返回一个散列，我们将把该散列存储在用户地址下的合同中，如下行所示:

```
await setContractHash(this.props.specificNetworkAddress, hash);
```

如果有任何错误，我们将提醒用户并删除加载动画:

```
this.setState({ loading: false });
alert("There was an error with the transaction.");
```

在函数的最后，我们将调用`this.fetchData()`来显示我们刚刚通过 IPS hash 保存到以太坊的数据。让我们来看看这个函数:

函数中的第一行，我们调用`getContractHash`并传递给它我们的以太坊地址，等待它返回我们的数据。一旦数据从智能契约返回，我们将把它存储到`contractDetails`变量中。

```
const contractDetails = await getContractHash(this.props.specificNetworkAddress);
```

接下来，我们将 IPFS 散列和时间戳存储到变量中:

```
const ipfsHash = contractDetails[0];
if (!ipfsHash) { return } //  if no hash store at this address
const timestamp = contractDetails[1].c[0];
```

然后我们获取与 IPFS 散列相关的数据:

```
const details = await getJSON(ipfsHash);
```

一旦有了这些，我们就将用户数据和时间戳设置到状态中，然后将这些数据显示在屏幕上:

```
this.setState({ ipfsData: details, loading: false, timestamp })
```

## 就这样结束了！

你现在已经有了开始你自己的以太坊 dapp 的基础工作知识！

要深入了解这些技术，请访问以下链接:

[React](https://reactjs.org/) —前端库
[Solidity](https://solidity.readthedocs.io/en/develop/) —用于构建运行在以太坊
[Truffle](http://truffleframework.com/) 上的智能合约的语言——编译、迁移和测试智能合约的框架。
I [PFS](https://ipfs.io/) —分散存储
[uPort](https://www.uport.me/) —身份管理、数字签名交易和轻松登录 dapps。

*此处表达的所有代码和观点**都是我自己的，而不是我雇主的观点。*

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)