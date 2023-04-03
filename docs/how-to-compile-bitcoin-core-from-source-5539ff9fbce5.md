# 如何从源码编译比特币核心

> 原文：<https://medium.com/coinmonks/how-to-compile-bitcoin-core-from-source-5539ff9fbce5?source=collection_archive---------0----------------------->

## 从 Linux 系统源代码编译比特币核心的简单指南

![](img/c2d2d6d56b9ad246377d54709b87d353.png)

Photo by [AronPW](https://unsplash.com/@aronpw?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 步骤 1:安装所有依赖项

更新您的系统，以便您的所有软件包都是最新的

```
$ sudo apt update
```

更新所有软件包后，安装所有依赖项

```
$ sudo apt-get install build-essential libtool autotools-dev automake pkg-config bsdmainutils python3 libssl-dev libevent-dev libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-test-dev libboost-thread-dev libminiupnpc-dev libzmq3-dev libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler git libsqlite3-dev ccache
```

# 步骤 2:安装比特币核心

安装完所有依赖项后，从 GitHub 下载比特币核心库。

```
$ git clone [https://github.com/bitcoin/bitcoin.git](https://github.com/bitcoin/bitcoin.git)
```

# 步骤 3:安装 Berkeley DB

输入您的比特币回购本地副本:

```
$ cd bitcoin
```

一旦你在你的比特币回购的根，运行:

```
$ ./contrib/install_db4.sh `pwd`
```

一旦 Berkeley DB 完成下载，输出应该如下所示:

db4 构建完成。

编译 bitcoind 时，运行`。/configure `以下列方式:

```
export BDB_PREFIX=’<PATH-TO>/db4'./configure BDB_LIBS=”-L${BDB_PREFIX}/lib -ldb_cxx-4.8" BDB_CFLAGS=”-I${BDB_PREFIX}/include”
```

记下终端中的输出，稍后会用到它。

# 第四步:编译比特币核心

默认情况下，您的比特币回购本地副本将与最近的代码更改保持同步，这可能不稳定。这就是为什么在编译代码之前，通过选择签出标签来选择特定的版本。

运行以下命令获取按最近更改排序的标记列表。

```
$ git tag | sort -V
```

通过运行命令选择一个类似 v0.21.0 的标记

```
$ git checkout v0.21.0
```

选择标记的分支后，运行以下命令

```
$ export BDB_PREFIX=’<PATH-TO>/db4'
Use the <PATH-TO> value from the output script from install_db4.sh$ /autogen.sh
$ ./configure BDB_LIBS=”-L${BDB_PREFIX}/lib -ldb_cxx-4.8" BDB_CFLAGS=”-I${BDB_PREFIX}/include” if using BDB 4.8, otherwise ./configure — with-incompatible-bdb
```

那就跑

```
$ make
```

或者，如果您有一个具有多个内核的 CPU，您可以通过以下方式利用它们并通过使用您的内核来加快处理速度

```
$ make -j "$(($(nproc)+1))"
```

# 步骤 5:测试构建

您可以通过运行以下命令来检查您的构建(您应该这样做)

```
$ make check
```

或者通过以下方式利用多重处理

```
$ make -j “$(($(nproc)+1))”
```

运行功能测试

```
$ test/functional/test_runner.py --extended
```

如果您想跳过一些测试，请忽略扩展测试。

# 第六步:安装比特币

此时，您可以通过以下方式开始使用比特币

```
$ src/bitcoind
```

或者，您可以通过运行以下命令来全局安装比特币

```
$ sudo make install
```

这将在你的系统中全局安装比特币。安装后，您可以从系统的任何地方调用 bitcoind 或 bitcoin-cli。

恭喜你，你刚刚从源代码中编译了比特币，你刚刚增加了你的设置的不可信任度。