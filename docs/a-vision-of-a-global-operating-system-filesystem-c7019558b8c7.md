# 全球操作系统的愿景:文件系统

> 原文：<https://medium.com/coinmonks/a-vision-of-a-global-operating-system-filesystem-c7019558b8c7?source=collection_archive---------0----------------------->

![](img/df75e61d868665582476f4eeb1c44f3f.png)

想象一下未来的以太坊 EVM 操作系统拥有自己的文件系统。EVM 将有权访问目录树并通过协商一致来设置特权。指向每个文件的**指针**在全局范围内可用。它甚至可以有透明的**驱动**到各种分散的文件存储: [Swarm](https://github.com/ethersphere) ， [IPFS](https://ipfs.io/) 等。

如何为这样的文件系统启动目录结构**呢？当然，您会希望从采用一种已经通过使用达成共识的结构开始，从桌面操作系统如 Linux、MacOS 或 Windows 开始。然后通过治理进一步发展结构。**

让我们看看当前使用的高级文件系统结构。

# Linux 目录结构

最新标准的更详细描述可在 http://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.html[找到。](http://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.html)

```
/          — Root directory
/bin       — Essential command binaries
/boot      — Static files of the boot loader
/dev       — Device files
/etc       — Host-specific system configuration
/lib       — Essential shared libraries and kernel modules
/media     — Mount point for removable media
/mnt       — Mount point for mounting a filesystem temporarily
/opt       — Add-on application software packages
/run       — Data relevant to running processes
/sbin      — Essential system binaries
/srv       — Data for services provided by this system
/tmp       — Temporary files
/usr       — Secondary hierarchy
/var       — Variable data files
/home      — User home directories (optional)
/root      — Home directory for the root user (optional)
/lib<qual> — Alternate format essential shared libraries (optional)
```

# MacOS 目录结构

最新标准 APFS 的更详细的描述可以在[这里](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html#//apple_ref/doc/uid/TP40010672-CH2-SW1)找到。

```
/Applications — Apps intended for use by all users of a computer
/Library      — App-specific (or system-specific) resources
/Network      — List of computers in the local area network
/System       — System resources required by macOS to run
/Users        — One or more user home directories
/bin          — Essential command-line binaries
/dev          — Essential device files - e.g. mount points
/etc          — Host-specific configuration files
/sbin         — Essential system binaries
/tmp          — Temporary files created by apps and the system
/usr          — Non-essential binaries, libs, header files, data
/var          — Variable data files - e.g. log files
/Volumes      — Contains a subdirectory for each mounted disk/Users/Applications — User-specific apps
/Users/Desktop      — Items on the user’s desktop
/Users/Documents    — User documents and files
/Users/Downloads    — Files downloaded from the Internet
/Users/Library      — User-specific app files
/Users/Movies       — User’s video files
/Users/Music        — User’s music files
/Users/Pictures     — User’s photos
/Users/Public       — Content the user wants to share
/Users/Sites        — Web pages used by the user’s personal site
```

# 分散文件系统

然而，当它们被安装在操作系统上时，这些文件夹中的大部分是为了由各种服务和应用程序在内部使用。它们甚至可能不会被普通用户接触到。

在我们的例子中，我们可以认为我们一直在使用第三方服务和应用，它们有一个明确的“**挂载点**”，并且是**独立于系统的**。我们不需要安装它们。这些**dapp**可以**托管在 Swarm 上，例如****，用户可以直接玩或者下载到自己的操作系统上。**

**我们现在可以忽略系统驱动的文件夹，更多地关注功能驱动的用户需求。我们的文件系统结构的起点可以是:**

```
/apps     — Pointers to independent applications
/data     — Pointers to public use data
/sources  — Pointers to various resource sources
```

# **个人用户文件**

**虽然上述文件夹保留了指向一般公共使用资源的指针，但每个用户的个人和私人资源都可以被**加密**和完全**离线托管**。它们可以被动态地**挂载**在文件系统应用的`/home`目录中，就像任何其他在链上注册的文件夹一样。**

**想象一下，您的设备上有加密数据，而这些数据只能用存储在钱包中的一组私钥来解密。如果你没有钱包，你仍然可以访问其余的文件，但不能访问加密的文件。如果你有钱包，它可以连接到 Swarm，连接到你的`/home`文件夹，同步你的文件并解密。**

**这是从你的操作系统加密系统升级而来的**，例如，苹果提供的。在 MacOS 上，你可以[用 FileVault](https://support.apple.com/en-us/HT204837) 加密你的整个硬盘，但这意味着**如果不知道密码或恢复密钥，你就无法访问任何东西**。密码本身可能是链条中的一个薄弱环节——用户通常会记住它，所以他们经常选择那些他们能记住并且很容易通过暴力找到的单词。****

**现在想象一下，不仅在你电脑的操作系统上安装你的私人文件夹，而且在你的浏览器上也安装。 [Brave](https://brave.com/funding-your-brave-wallet/) 和 [Opera](https://blogs.opera.com/desktop/2019/04/opera-60-reborn-3-web-3-0-vpn-ad-blocker/) 已经开始集成加密货币浏览器钱包，因此拥有一个连接到你的浏览器钱包并根据你的私钥解密你的文件的文件系统应用程序现在在技术上是合理的。如果浏览器足够可靠，你可以在任何电脑上复制你的工作环境。**

# **全局文件系统**

**但是，让我们回到我们的一般文件系统的想法。现在有几点值得强调:**

*   **您可以对**公共**高级文件夹进行**链上**注册&文件管理**
*   **您可以对不需要共识的**私有**资源和**公共**资源进行**链外**文件管理**
*   **如果需要，链外资源可以有一个指向它们的**指针****

## **为什么我们需要一个公共管理的文件系统组件？**

**通过将文件系统的主干放在链上，用户可以**彼此协作**，并且他们可以为每个文件或文件夹设置不同的共识规则。如果我们有一个全球系统来注册我们的资源，我们可以将它们整合到一个更大的生态系统中。这也是 Github 为 git 所做的事情。具有权限和版本控制的文件系统是 git 本身的基础。**

**文件系统可以用于实际的**治理资源**。想想公开可用的政府法律，甚至是针对不同人群的规则。你最终可以添加插件来投票、讨论和计划这些规则提案——无论是在线还是离线。**

# **/应用程序**

**文件系统可以存储指向我们可以使用的各种应用程序的指针。这是一个通用的**应用**注册表，用户可以在这里搜索和选择他们想要添加到个人文件系统的应用，作为软链接。**

**想象一下，在浏览器中打开你的工作空间，手边有你喜欢的/ **标有星号的应用**，以及一个简化的**应用类别树**，这取决于你选择了什么样的应用。
您可以随时转到公共应用类别树并添加更多。**

**然后，你可以打开一个应用程序(托管在 Swarm 上)，使用你的浏览器钱包连接到它并使用它。你可以在你的个人文件系统中存储该应用产生的任何副作用。还记得 Linux / MacOS 的文件夹结构吗？应用通过知道在哪里存储各种配置、缓存、数据等来与操作系统集成。文件。你的 Swarm 应用也可以做到这一点，并将这些资源存储在你的个人文件系统中。**

# **/数据**

**如上所述，我们需要一个地方来存储指向公共可用数据的指针——治理建议、研究文章、统计数据等。**

**这个文件夹也可以存在于你的`/home/data`中，保存指向你的应用数据和任何你想要的东西的指针。**

# **/来源**

**该文件夹可以保存指向使您的环境运行的实际代码源的指针。库、应用、智能合约等。这些指针将在所有需要它们的地方被引用。还记得`/apps`文件夹吗？每个应用程序可以在内部使用来自`/sources`的指针，并与其他应用程序共享资源(就像 MacOS 使用`/Library/Frameworks`)。**

# **还有一点**

**这个愿景是由我们在 **dType** 上的工作启发而来的——EVM 的一个分散型系统。在这里了解一下:[https://medium . com/@ loredana . cir stea/dtype-decentralized-type-system-functional-programming-on-ether eum-4f 7666377 c 9 f](/@loredana.cirstea/dtype-decentralized-type-system-functional-programming-on-ethereum-4f7666377c9f)。**

**我们目前正在研究文件系统类型和适配器，它们可以被项目全局重用。进行中的工作:【https://github.com/pipeos-one/dType-fs】T2。但是这将在下一篇博客文章中详细讨论。**

****想要提供反馈、推荐类型还是只是打个招呼？头转向**[](https://gitter.im/pipeos-one/dType)****。******

> ****[直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)****

****[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)****