# 在不到 30 分钟的时间内，将基于 IPFS 的可编辑用户配置文件添加到您的 dapp 中

> 原文：<https://medium.com/coinmonks/add-editable-ipfs-based-user-profiles-to-your-dapp-in-less-than-30-mins-abae8c9a05e6?source=collection_archive---------0----------------------->

![](img/3ef26275a8c46bfcd2a251dcbb8ec164.png)

[I](https://icons8.com)llustration by [Ouch.pics](https://icons8.com)

## 为 Web3 用户创造更人性化的体验

用户配置文件在 Web2.0 应用程序中无处不在，我们每天都在使用，以至于我们很少考虑它们，但是很少有分散的应用程序使用它们。在 3Box，我们希望改变这种情况，为 dapps 带来更人性化的用户体验。

## 3Box 的个人资料编辑插件介绍

这就是为什么我们创建了我们最新的嵌入式 UI 组件， [**个人资料编辑插件**](https://docs.3box.io/build/plugins/profile-edit) (这里有[演示](https://3box.github.io/3box-profile-edit-react/examples/dist/))。使用该组件，您可以在大约 10-30 分钟内将**用户控制的可编辑配置文件添加到分散的 react 应用程序中**。我们制作的 dapps 越社会化，用户就会越想使用它们；没有理由让你的用户在十六进制代码中挣扎！

*如果你更喜欢通过视频学习，看看这个教程👇*

3Box Profile Edit plugin video tutorial

# 1.与以太坊钱包集成

[**3Box**](https://3box.io) 使用加密认证，这意味着加密密钥用于管理 3Box 数据库认证和身份。这些 3Box 密钥对每个 3Box 用户都是唯一的，并且是在钱包的私钥(我们从不直接接触它们)签署消息时确定性地在客户端生成的。这意味着当使用 3Box 时，你需要将你的前端连接到一个以太坊钱包。这使得 3Box 可以使用用户的以太坊地址和钱包的以太坊提供商。欲了解更多信息，请参阅我们的[架构博客文章](/3box/3box-architecture-a3e35c82e919)。

*3Box 支持* ***所有标准以太坊钱包*** *，但对于本教程我们将用* [*元掩码*](https://metamask.io) *进行构建。*

## 设置您的应用

为了入门，我用了这个用 Create React app 内置的 [**样板**](https://github.com/RachBLondon/metamask-3box-boilerplate) 。这将通过启用由 MetaMask 注入到前端的以太坊提供者来设置前端以与 3Box 集成(最终用户将需要安装这个)。这使我们能够访问用户的元掩码地址，然后保存到 react 状态。这发生在以下函数中:

```
async getAddressFromMetaMask() {
    if (typeof window.ethereum == "undefined") {
      this.setState({ needToAWeb3Browser: true });
    } else {
      const accounts = await window.ethereum.enable();
      this.setState({ accounts });
    }
  }async componentDidMount() {
    await this.getAddressFromMetaMask();	
}
```

当页面在`componentDidMount`中加载时，这个函数被直接调用。这是实现起来最简单的模式，我们必须等到页面装载后才能调用该函数，因为在此之前，MetaMask 不会将提供者注入页面。

# 2.用 3Box 认证并打开空间

## 安装 3Box.js

现在我们已经启用了提供者，并且可以访问用户的以太坊地址，我们可以开始使用`3box.js`了。如果您还没有这样做，请确保您`npm i 3box`并将库导入到文件顶部的 as 框中(在 JS 中变量名不能以数字开头)。然后我们可以返回到`componentDidMount`函数，在我们调用`getAddressFromMetaMask`之后，我们可以添加下面几行:

```
async componentDidMount() {
    await this.getAddressFromMetaMask();  const box = await Box.openBox(this.state.accounts[0], window.ethereum);
		const space = await box.openSpace('edit-profile-plugin');
		this.setState({ space, box });
}
```

## 认证 3 框

我们需要做的第一件事就是对用户进行认证。为此，我们将调用 3Box 库中可用的`openBox`方法。不要担心，如果用户还没有 3Box 帐户，我们会为他们创建一个。这是用户从其钱包中的签名消息生成加密 3 盒密钥的阶段。这就是为什么我们需要传入用户的以太坊地址和提供者。

## 验证你的应用空间

使用`openBox`认证 3Box 后，下一步是**认证一个空间**。**空间**是在用户的 3Box 中用于特定应用存储的命名空间区域。我们可以用`openSpace`方法做到这一点。这只需要一个参数，空间的名称。此名称将用于命名您的共享空间，因此请选择一个独特而具体的名称。对于本教程，`edit-profile-plugin`就可以了。在我们使用**3 框**和我们的应用程序的**空间**进行身份验证后，我们可以保存两者以供以后使用。

# 3.添加个人资料编辑插件

接下来我们可以`npm i 3box-profile-edit-react`并将它导入到我们文件的顶部。在我们的渲染函数中，我们可以开始使用组件。

要以最简单的形式使用组件，我们只需要下面的代码。

```
{this.state.space && this.state.box && (
          <EditProfile
            box={this.state.box}
            space={this.state.space}
            currentUserAddr={this.state.accounts[0]}
          />
        )}
```

这里我们添加了一个条件来确保组件在**3 框**和**空间**被认证之前不会呈现。然后我们进去:

*   用户的地址
*   认证空间和 3Box(盒子)

![](img/bd035285f1a64988da4e7d4acd235077.png)

现在有了一个工作配置文件编辑组件！可以添加定制字段和重定向功能，以适应您的用例([检查文档](https://docs.3box.io/build/plugins/profile-edit#try-the-demo-here))。该组件允许用户直接从前端应用程序编辑分布式的、用户控制的配置文件。**这是一个不错的方法，可以让你的分散式应用程序在短时间内更人性化！**

Edit Profile 是我们的**嵌入式 react 插件**系列中的最新产品，旨在为分布式应用程序带来更具吸引力的 UX。查看我们的 [**个人资料 Hove**](https://docs.3box.io/build/plugins/profile-hover) **r** 、 [**聊天框**](https://docs.3box.io/build/plugins/chatbox) 和 [**评论**](https://docs.3box.io/build/plugins/comments) 插件，了解更多将 web2.0 用户体验带入 web3.0 应用的方法！

*我们很想听听你在建什么，加入我们的* [*不和谐*](https://chat.3box.io/) *频道吧！*

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)[![](img/e9dbce386c4f90837b5db529a4c87766.png)](https://coincodecap.com)