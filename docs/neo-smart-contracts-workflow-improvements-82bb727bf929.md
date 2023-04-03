# NEO 智能合同:工作流程改进

> 原文：<https://medium.com/coinmonks/neo-smart-contracts-workflow-improvements-82bb727bf929?source=collection_archive---------9----------------------->

![](img/c054ab9c2af4518e3a50cde699e037d6.png)

在本帖中，我将介绍几个快速设置技巧，它们可以通过改进您的开发设置工作流程来帮助您节省时间。

如果你还没有浏览 hello world 和 dev 环境设置教程，我建议你在继续这篇文章之前快速浏览一下。

[Hello World 智能合约教程](/@nickfujita/neo-smart-contracts-tutorial-helloworld-13ecc19b31fe)

[开发环境设置教程](/@nickfujita/neo-dev-environment-setup-tutorial-e495f5364ada)

让我们面对它，腕管是真实的，尽管我希望你回来准备我的博客文章，但更重要的是你有一个快速高效的工作流程，使你和你的合同代码之间的摩擦最小化。

![](img/78a1aa1ba09b2ecd5eacbb1c87bdd00f.png)

# Bash 脚本

这就是 bash 脚本派上用场的地方。如果您以前没有使用过它们，它们只是终端中的快捷方式，我们可以使用它们来引用更大的命令。我们可以在本地机器的 docker 容器中编辑根目录中这些文件的列表。

# 本地机器

要在本地计算机上访问您的`.bashrc`,您可以使用您最喜欢的文本编辑器在根目录下打开它。我的情况是:

```
atom ~/.bashrc
```

在这里，我们可以添加我们的别名命令。第一个是旋转 docker 容器。虽然这通常只做一次，但你最终会耗尽汽油，不得不重新创建容器。因此，手头有了这个，你就可以去网上复制意大利面了。

```
alias neodocker="docker run -d --name neo-privatenet -p 20333-20336:20333-20336/tcp -p 30333-30336:30333-30336/tcp -v ~/Documents/smartContracts:/neo-python/smartContracts cityofzion/neo-privatenet"
```

值得注意的是，这与 dev 环境设置教程中的版本有所不同。由于它现在是我们的 bashrc 的一部分，我们需要将卷引用路径更新到我们将要共享的本地目录。我的情况是从`"$(pwd)"`更新到`~/Documents/smartContracts`

下一个别名是当您需要 SSH 到 docker 容器时的快捷方式。

```
alias neossh="docker exec -it neo-privatenet /bin/bash"
```

现在，在我们的本地机器上，如果还没有创建容器，我们可以简单地使用调用`neodocker`来旋转容器，并使用`neossh`来插入。由于重新创建容器非常少见，所以大多数时候您可能会使用`neossh`。

![](img/a0cdd46372b5e70bcb017c274b3fb83e.png)

# 通用 Docker 脚本

以下是我使用的另外几个随机 docker 命令:

```
alias dps="docker ps"alias dpsa="docker ps -a"
```

请小心使用下面的 2 条，因为如果你不小心的话，你会无意中关闭容器

```
drmfa () { docker rm -f $(docker ps -aq)}
```

用`drmf {container_id}`调用

```
drmf () { docker rm -f "$1"}
```

最后，如果您不喜欢任何命令的缩写，最棒的是因为它们都在 bashrc 文件中，您可以自由地将它们更新为最适合您的格式。

一旦你把这些都设置好了，你就应该像一个疯子一样在航站楼里飞来飞去，并且没有任何借口不直接开始写你的合同代码，就像我现在写这篇文章所做的:p

如果你觉得这个教程有帮助，请在:

```
NEO/GAS/NEP5 tokens: AR8rRBxgWw5siKsp1dUmfTLy6QQTjcqoqBETH/ERC20 tokens: 0x575970777095575fDd12d9cD3B105b41D8DD1344NANO/XRB: xrb_3rpmim57tqbrb8hhjfzm1x5uk135yhmgqoj7j7wsufpo53e3ad5k93ggy1cdLTC: M8nZ7FwJXSMAkAfiGmTKjbB7MWpdwsp52dBTC: 3LcA1ztxFjghexRNWPyGXA59VFU3gF4PTzBCH: 1LXLiF7SjWGday6gm61hzbYHKbSAMevwXo
```

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)