# Android 上的 Geth

> 原文：<https://medium.com/coinmonks/geth-on-android-f6392fe81434?source=collection_archive---------1----------------------->

让我们学习如何运行一个 Geth 节点，并在原生 Android 应用程序中与之交互。我们将启动一个 Geth 节点，创建一个新的 Ethereum 帐户，并将有关该帐户的信息安全地存储在设备上。如果你想简单了解以太坊移动世界，以太坊的 Github 上有相关信息:

[](https://github.com/ethereum/go-ethereum/wiki/Mobile:-Introduction) [## 以太坊/围棋以太坊

### 以太坊-以太坊协议的官方 go 实现

github.com](https://github.com/ethereum/go-ethereum/wiki/Mobile:-Introduction) 

您可以在我的 Github 上找到与本文相关的所有代码:

[](https://github.com/drkhannah/GethAndroid) [## drkhannah/GethAndroid

### 原生 Android 上的 GethAndroid - Geth 节点

github.com](https://github.com/drkhannah/GethAndroid) 

# 在你的应用构建中获得 Geth 依赖。格雷德

您首先需要将 Geth 作为一个依赖项添加到您的应用程序的**版本中。格雷德**。

```
// Your App's build.gradledependencies {
    ...*// Geth* compile **'org.ethereum:geth:1.7.3'**;
}
```

我们还将连接到互联网，因此请确保您的应用在其 **AndroidManifest.xml** 文件中获得使用互联网的权限

```
// AndroidManifest.xml<**uses-permission android:name="android.permission.INTERNET"** />
```

# 在单例中保存第一个节点:

你需要做的第一件事是在“主”以太网上启动一个 Geth 节点。该节点需要在应用程序的整个生命周期中保持活动和运行，因此我们将在单例中保存节点实例。在开始节点本身之前，让我们先创建我们的单例。你会在我在本文顶部链接的项目代码中看到，我已经创建了一个**GethNodeHolder.java**类，这是我们的单例。

```
// GethNodeHolder.java**public class** GethNodeHolder {

    **private** Node **node**;
    **private** Account **account**;
    **private static** GethNodeHolder *instance* = **null**;

    **private** GethNodeHolder() {
    }

    **public static** GethNodeHolder getInstance() {
        **if** (*instance* == **null**) {
            *instance* = **new** GethNodeHolder();
        }
        **return** *instance*;
    }

    **public** Node getNode() {
        **return node**;
    }

    **public void** setNode(Node node) {
        **this**.**node** = node;
    }

    **public** Account getAccount() {
        **return account**;
    }

    **public void** setAccount(Account account) {
        **this**.**account** = account;
    }

}
```

这个 singleton 不仅有方法来**设置**和**获取**一个 Geth 节点，而且还有方法来设置和**获取**一个我们将在节点启动并运行后创建的以太坊帐户。

# **启动一个 Geth 节点**

让我们在**MainActivity.java**文件的 **onCreate()** 方法中开始一个 Geth 节点。

```
// MainActivity.java @Override
**protected void** onCreate(Bundle savedInstanceState) {
    **super**.onCreate(savedInstanceState);
    setContentView(R.layout.***activity_main***);

    **try** {
        NodeConfig nc = **new** NodeConfig();
        Node node = Geth.*newNode*(getFilesDir() + **"/.ethNode"**, nc);
        node.start();

        GethNodeHolder gethNode = GethNodeHolder.*getInstance*();
        gethNode.setNode(node);

    } **catch** (Exception e) {
        Log.*e*(**"error: "**, e.getMessage());
        e.printStackTrace();
    }
}
```

要创建一个 Geth 节点，首先需要将一个 **NodeConfig** 传递给 Geth 的 **newNode()** 方法。 **newNode()** 方法创建一个新的 Geth 节点，并将其 **NodeConfig** 存储在应用程序默认文件系统的一个名为 **"ethNode"** 的文件中。然后我们通过调用它的 **start()** 方法来启动节点。这将启动节点并将其连接到“主”以太网

一旦节点被启动，我们就获得我们的**GethNodeHolder**singleton 的一个实例，并调用它的 **setNode()** 方法，传入我们新启动的节点。这确保了节点将被存储在我们的**GethNodeHolder**singleton 中，并在应用程序的整个生命周期中保持活跃。

# **在“主”网络上创建一个新的以太坊账户**

现在我们已经启动了一个节点，让我们通过在“主”以太网上创建一个新的以太网帐户来与它交互。我们将使用加密的 **Geth** **密钥库**将该账户的信息安全地存储在设备上。我们还将创建一个非常简单的用户界面，带有一个创建新帐户的按钮。让我们先看看 UI 部分。这只是一个简单的按钮，它将调用一个 **createAccount()** 方法，我们稍后将定义这个方法。它还有一个文本视图，稍后将显示新创建的帐户的地址。

```
// activity_main.xml*<?***xml version="1.0" encoding="utf-8"***?>* <**android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="software.toybox.derek.geth.MainActivity"**>

    <**Button
        android:id="@+id/create_acc_btn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="createAccount"
        android:text="Create Account"
        app:layout_constraintBottom_toTopOf="@+id/acc_display_textview"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"** />

    <**TextView
        android:id="@+id/acc_display_textview"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Account Display"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/create_acc_btn"** />

</**android.support.constraint.ConstraintLayout**>
```

接下来让我们看看当用户点击**“创建帐户”**按钮时调用的 **createAccount()** 方法。

```
// MainActivity.java**public void** createAccount(View button) {

    **try** {
        GethNodeHolder gethNodeHolder = GethNodeHolder.*getInstance*();
        Node gethNode = gethNodeHolder.getNode();

        **if** (gethNode != **null**) {

            KeyStore ks = **new** KeyStore(getFilesDir() + **"/keystore"**, Geth.***LightScryptN***, Geth.***LightScryptP***);
            Account newAccount = ks.newAccount(**"Password"**);

            Log.*d*(**"account address: "**, newAccount.getAddress().getHex());
            gethNodeHolder.setAccount(newAccount);

            Account account = gethNodeHolder.getAccount();
            **accDisplayTextView**.setText(**"Here is your Account Address: "** + account.getAddress().getHex());
        }
    } **catch** (Exception e) {
        Log.*d*(**"error: "**, e.getMessage());
        e.printStackTrace();
    }
}
```

该方法首先获取**GethNodeHolder**singleton，然后获取我们在其中设置的节点。然后它创建一个**新的 KeyStore()** 实例，这是一个安全的 **Geth Keystore** 来保存设备上的帐户信息。第一个参数是希望在设备上创建密钥库文件的路径。我们使用应用程序的默认文件目录，并创建一个名为 **"/keystore"** 的目录。您还将看到传递给 **new KeyStore()** 构造函数、 **Geth 的另外两个参数。*light cryptn***和 **Geth。*light cryptp。*** 这些参数表示我们只想从以太坊区块链下载*标头*，这些标头与我们决定存储在这个密钥库中的任何以太坊帐户相关联。然后，我们通过调用 **KeyStore** 的 **newAccount()** 方法创建一个新帐户，并向其传递一个硬编码的密码**“password”**。现在我们创建了新的帐户，我们调用我们的 **GethNodeHolder** 的 **setAccount()** 方法，并将其传递给我们新创建的以太坊帐户。然后为了好玩，我们从 **GethNodeHolder** 中取回账户(主要是为了演示)。最后，我们向用户显示帐户的地址。

# **结论**

写这篇文章让我意识到在移动设备上使用 Geth 的文档并不多。这项技术太新了，docs 不在那里的最大原因是因为技术本身还不存在。我已经意识到以太坊技术和区块链技术作为一个整体还有很多问题需要解决。

不过，我确实发现了一个为 Android 上的以太坊开发的框架:

[](https://ethereum-android.com/) [## 以太坊 Android -您的以太坊移动门户

### 安卓以太坊应用平台和钱包

ethereum-android.com](https://ethereum-android.com/) 

请继续关注这篇文章的未来更新，很可能还有一篇关于如何在 iOS 上做这件事的类似文章。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)