# 设置 EOS 区块链

> 原文：<https://medium.com/coinmonks/setting-up-the-eos-blockchain-5cd98f99eb84?source=collection_archive---------7----------------------->

![](img/8329bba9576e4fee79f0b596948a9b72.png)

# 背景

本文使您能够使用 EOSIO 软件安装来设置您的系统。在这之后，人们将能够把 C++合同编译成 ABI 文件并上传到区块链。

# 先决条件

*   打开 cmd。
*   `>pushd directory-where-eos-folder-is-to-be-kept`
*   `>bash`
*   现在，您已经准备好执行下面的步骤。

# 装置

*   **克隆 github 库**

`$ git clone https://github.com/EOSIO/eos.git --recursive`

![](img/77d03b82fa49a27a8dfc2e81245d4fba.png)

*   **转到 eos 文件夹**

`$ cd eos`

![](img/b74b49fb62174ae2fcda4d94217dabe9.png)

*   **子模块检查**

`$ git submodule update --init --recursive`

*   **最新版本**

https://github.com/EOSIO/eos/releases/latest

它会将您带到最新的发布版本页面(如 1.0.3 版、1.0.2 版、1.0.1 版等)。从那里获取版本号。

`$ git checkout tags/v1.0.3`

![](img/5f35696bec2925023b7331b94fed30bb.png)

*   **构建工具**

`$ ./eosio_build.sh`

![](img/d2967b407230dd27f7ed9df8836a1f76.png)

*   *升压安装*

![](img/e28a0d9d10fba51bf81a867943178501.png)

*   *Mongodb & Mongo C++驱动安装*

![](img/f98764a5b7489e334ff2a19969cd4f52.png)

*   *secp256kl 安装*

![](img/849bd95f252d3bbfcb41ea3d3d43c3fa.png)

*   *WASM 安装*

![](img/85cdf1b332eb5081775592e9c29c95f3.png)

*   *EOSIO 安装*

![](img/bcaeaabde0a0db2ca4b1abd74a057028.png)

*   *制作安装*

`$ cd build`

`$ sudo make install`

![](img/98deabbf9fbb185b40a1d27446c51047.png)

# EOSIO 系统架构

![](img/130d11523bc8856e63f6273dfe3d5bd1.png)

*   `cleos`

EOS 区块链的命令行(CLI)工具

![](img/e9824088f388d6490b20b056de373760.png)

*   `nodeos`

与区块链互动。

首先，找到“genesis.json”文件，然后运行命令。

![](img/059b5c4c3b10e3b4f96cfb656709240c.png)

*   `keosd`

用于访问钱包

![](img/02f2c6a5692216b012f19629cb0ddd3e.png)

现在，您可以继续在本地编译合同。

***就是这样！！..***

**敬请关注更多此类教程。**

https://github.com/abhi3700/My_Learning_EOS github-