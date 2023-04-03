# EOSIO 智能合同教程

> 原文：<https://medium.com/coinmonks/eosio-smart-contracts-tutorial-f22c3bb364d9?source=collection_archive---------0----------------------->

![](img/39979dad47644b5c2a4af3eea4e3e2c8.png)

[source](https://www.investinblockchain.com/eosio-dawn/)

# 0.必需的背景知识

*   **C / C++体验**
*   **Linux / Mac OS 体验**
*   **命令行知识**

# 1.EOSIO 智能合约的基础知识

## 1.1.通信模型

EOSIO 智能合约以动作和共享内存数据库访问的形式相互通信。合同中可以定义两种通信模式:

*   **内嵌**。保证 Inline 与当前事务一起执行或展开；无论成功或失败，都不会传达任何通知。内联操作具有与原始事务相同的作用域和权限。
*   **延期**。延期将由制作人自行决定；可以传递通信结果，也可以简单地超时。延期可以到达不同的范围，并携带发送它们的契约的权限。

> [发现并回顾最佳区块链软件](https://coincodecap.com)

## 1.2.行动与交易

一个动作代表一个操作，而一个事务是一个或多个动作的集合。一个契约和一个账户以行动的形式交流。动作可以单独发送，或者以组合的形式发送，如果它们打算作为一个整体执行的话。

## 1.3.交易确认

接收到一个事务散列并不意味着该事务已经被确认，它只意味着该节点无误地接受了它，这也意味着有很大概率其他生产者会接受它。

通过确认，您应该可以在交易历史记录中看到该交易及其包含的块号。

# 2.启动私有区块链

您可以使用以下命令启动自己的单节点区块链:

```
$ nodeos -e -p eosio --plugin eosio::wallet_api_plugin --plugin eosio::chain_api_plugin --plugin eosio::account_history_api_plugin
```

假设一切正常，您应该每 0.5 秒看到一条块生成消息。

这意味着您当地的区块链是活的，生产块，并准备使用。

# 3.创建钱包

钱包是授权在区块链上进行操作所必需的私有密钥的存储库。这些密钥存储在使用为您生成的密码加密的磁盘上。该密码应存储在安全的密码管理器中。

```
$ cleos wallet create
Creating wallet: default
Save password to use in the future to unlock this wallet.
Without password imported keys will not be retrievable.
"PW5JuBXoXJ8JHiCTXfXcYuJabjF9f9UNNqHJjqDVY7igVffe3pXub"
```

出于这个简单开发环境的目的，您的钱包由您的本地`nodeos`通过我们在启动`nodeos`时启用的`eosio::wallet_api_plugin`来管理。无论何时重启`nodeos`，你都必须先解锁钱包，然后才能使用里面的钥匙。

```
$ cleos wallet unlock --password PW5JuBXoXJ8JHiCTXfXcYuJabjF9f9UNNqHJjqDVY7igVffe3pXub
Unlocked: default
```

在命令行上直接使用密码通常是不安全的，因为密码会被记录到 bash 历史记录中，所以您也可以在交互模式下解锁:

```
$ cleos wallet unlock
password:
```

为了安全起见，通常不使用钱包时最好把它锁上。要在不关机的情况下锁定您的钱包，您可以:

```
$ cleos wallet lock
Locked: default
```

在本教程的剩余部分，您需要解锁您的钱包。

将`eosio`账户的主密钥导入您的钱包。主密钥可以在`nodeos`的 config 文件夹中的`config.ini`文件中找到。在本例中，使用了默认的 config 文件夹。在 MacOS 上，这将是在`~/Library/Application Support/eosio/nodeos/config`。

```
$ cleos wallet import 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
imported private key for: EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
```

# 4.加载 Bios 合同

Bios 合同使您能够直接控制其他帐户的资源分配，并访问其他特权 API 调用。在公共区块链中，此契约将管理令牌的堆叠和取消堆叠，以便为 CPU 和网络活动预留带宽，并为契约预留内存。

```
$ cleos set contract eosio build/contracts/eosio.bios -p eosio
Reading WAST/WASM from build/contracts/eosio.bios/eosio.bios.wast...
Assembling WASM...
Publishing contract...
executed transaction: bb7fb236627de8ff4247064cd8b1bf8f266b9385491a94bd6ca26d26145fbe75  3288 bytes  2200576 cycles
#         eosio <= eosio::setcode               {"account":"eosio","vmtype":0,"vmversion":0,"code":"0061736d0100000001581060037f7e7f0060057f7e7e7e7e...
#         eosio <= eosio::setabi                {"account":"eosio","abi":{"types":[],"structs":[{"name":"set_account_limits","base":"","fields":[{"n...
```

代码定义了契约如何运行，abi 描述了如何在参数的二进制和 json 表示之间进行转换。该命令序列的结果是`cleos`生成了一个包含两个动作`eosio::setcode`和`eosio::setabi`的事务。可以理解为:由`eosio`定义的动作`setcode`由与`{args...}`的`eosio`契约执行

这个调用的最后一个参数是`-p eosio`。这告诉`cleos`使用`eosio`帐户的活动授权签署该操作，即使用我们之前导入的`eosio`帐户的私钥签署该操作。

# 5.创建帐户

既然我们已经设置了基本系统合同，我们可以开始创建我们自己的帐户。我们将创建两个帐户，`user`和`tester`，并且我们需要将一个密钥与每个帐户相关联。在本例中，两个帐户将使用相同的密钥。

为此，我们首先为帐户生成一个密钥。

```
$ cleos create key
Private key: 5JxykKNaPZjhBETmfip8aDuP3UaZrCNBBznx17qFo4Sbyk7pE9Q
Public key: EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ
```

然后我们将这个密钥导入我们的钱包:

```
$ cleos wallet import 5JxykKNaPZjhBETmfip8aDuP3UaZrCNBBznx17qFo4Sbyk7pE9Q
imported private key for: EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ
```

## 5.1.创建两个用户帐户

接下来，我们将创建两个帐户，`user`和`tester`，使用我们在上面创建和导入的密钥。`create account`子命令需要两个密钥，一个用于 OwnerKey(在生产环境中应该保持高度安全),另一个用于 ActiveKey。

```
$ cleos create account eosio user EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ
executed transaction: 8aedb926cc1ca31642ada8daf4350833c95cbe98b869230f44da76d70f6d6242  364 bytes  1000 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"user","owner":{"threshold":1,"keys":
​
$ cleos create account eosio tester EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ
executed transaction: 414cf0dc7740d22474992779b2416b0eabdbc91522c16521307dd682051af083 366 bytes  1000 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"tester","owner":{"threshold":1,"keys":
```

我们可以查询由我们的密钥控制的所有帐户:

```
$ cleos get accounts EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ
{
  "account_names": [
    "tester",
    "user"
  ]
}
```

# 6.部署令牌合同

`eosio.token`契约允许创建许多不同的令牌，它们都运行在同一契约上，但可能由不同的用户管理。在部署令牌合约之前，我们必须创建一个帐户来部署它。

```
$ cleos create account eosio eosio.token EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ
```

然后我们可以部署在`${EOSIO_SOURCE}/build/contracts/eosio.token`中找到的合同

```
$ cleos set contract eosio.token build/contracts/eosio.token -p eosio.token
Reading WAST...
Assembling WASM...
Publishing contract...
executed transaction: 528bdbce1181dc5fd72a24e4181e6587dace8ab43b2d7ac9b22b2017992a07ad  8708 bytes  10000 cycles
#         eosio <= eosio::setcode               {"account":"eosio.token","vmtype":0,"vmversion":0,"code":"0061736d0100000001ce011d60067f7e7f7f7f7f00...
#         eosio <= eosio::setabi                {"account":"eosio.token","abi":{"types":[],"structs":[{"name":"transfer","base":"","fields":[{"name"...
```

## 6.1.创建 EOS 令牌

您可以查看`contracts/eosio.token/eosio.token.hpp`定义的`eosio.token`界面

要创建一个新令牌，我们必须调用带有适当参数的`create(...)`动作。此命令将使用最大供应的符号来唯一地将此令牌与其他令牌区分开。发行人将有权调用发行和/或执行其他操作，如冻结、召回和将所有者列入白名单。

使用位置参数调用此方法的简明方法是:

```
$ cleos push action eosio.token create '[ "eosio", "1000000000.0000 EOS", 0, 0, 0]' -p eosio.token
executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12  260 bytes  1000 cycles
#   eosio.token <= eosio.token::create          {"issuer":"eosio","maximum_supply":"1000000000.0000 EOS","can_freeze":0,"can_recall":0,"can_whitelis...
```

或者，使用命名参数以更详细的方式调用此方法:

```
$ cleos push action eosio.token create '{"issuer":"eosio", "maximum_supply":"1000000000.0000 EOS", "can_freeze":0, "can_recall":0, "can_whitelist":0}' -p eosio.token
executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12  260 bytes  1000 cycles
#   eosio.token <= eosio.token::create          {"issuer":"eosio","maximum_supply":"10
```

该命令创建了一个新的令牌`EOS`，精度为 4 分米，最大供应量为 1000000000.0000 EOS。

## 6.2.向帐户“用户”颁发令牌

现在我们已经创建了令牌，发行者可以向我们之前创建的帐户`user`发行新的令牌。

```
$ cleos push action eosio.token issue '[ "user", "100.0000 EOS", "memo" ]' -p eosio
executed transaction: 822a607a9196112831ecc2dc14ffb1722634f1749f3ac18b73ffacd41160b019  268 bytes  1000 cycles
#   eosio.token <= eosio.token::issue           {"to":"user","quantity":"100.0000 EOS","memo":"memo"}
>> issue
#   eosio.token <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
>> transfer
#         eosio <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
#          user <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
```

这次输出包含几个不同的动作:一个发布和三个转移。虽然我们签署的唯一动作是`issue`，但是`issue`动作执行了一个“内联传输”并且“内联传输”通知了发送者和接收者账户。输出指示所有被调用的操作处理程序、它们被调用的顺序，以及操作是否生成了任何输出。

从技术上讲，`eosio.token`合同可以跳过`inline transfer`合同，选择直接修改余额。然而，在这种情况下，`eosio.token`契约遵循我们的令牌约定，要求所有账户余额都可以通过引用它们的转账操作的总和来导出。它还要求通知资金的发送者和接收者，以便他们可以自动处理存款和取款。

如果您想看到实际广播的事务，您可以使用`-d -j`选项来指示“不广播”和“将事务作为 json 返回”。

```
$ cleos push action eosio.token issue '["user", "100.0000 EOS", "memo"]' -p eosio -d -j
{
  "expiration": "2018-04-25T23:50:50",
  "region": 0,
  "ref_block_num": 20835,
  "ref_block_prefix": 3876493376,
  "max_net_usage_words": 0,
  "max_kcpu_usage": 0,
  "delay_sec": 0,
  "context_free_actions": [],
  "actions": [{
      "account": "eosio.token",
      "name": "issue",
      "authorization": [{
          "actor": "eosio",
          "permission": "active"
        }
      ],
      "data": "00000000007015d640420f000000000004454f5300000000046d656d6f"
    }
  ],
  "signatures": [
    "EOSJwTiuUfr3iD4tTAeVYU8KP4GrV6jCyFiqPVZtWMKeisR7pr8moXYAaxhyf4VAhacPFea57eMKLbFCTaBxzBxqKiqLNBc9X"
  ],
  "context_free_data": []
```

## 6.3.将代币转移到帐户“测试者”

现在帐户`user`有代币了，我们将转移一些到帐户`tester`。我们指出`user`使用权限参数`-p user`授权了这个动作。

```
$ cleos push action eosio.token transfer '[ "user", "tester", "25.0000 EOS", "m" ]' -p user
executed transaction: bd6da0a70e386a5302d02c481d80a7bd19409169d0bab035090b1d3867d29391  256 bytes  111616 cycles
#   eosio.token <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
>> transfer
#          user <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
#        tester <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
```

# 7.部署交换合同

类似于上面显示的例子，我们可以部署`exchange`契约。`exchange`合同提供了创造和交易货币的能力。假设这是从 EOSIO 源代码的根目录运行的。

```
$ cleos create account eosio exchange EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ
executed transaction: 6337ac311a5df1fb9ed9b1f8728b2ea67b2bc9663c06cf6e0797d12d7280572e  352 bytes  102400 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"exchange","owner":{"threshold":1,"keys":[{"key":"EOS5zgUiXJxDNvP8HJRrzXdS...
```

## 7.1.部署 Eosio.msig 合同

`eosio.msig`契约允许多方异步签署一个交易。EOSIO 在基本级别上支持多重签名(multisig ),但它需要一个同步边信道来传送和签名数据。`Eosio.msig`是一种更加用户友好的异步提议、批准并最终发布多方同意的交易的方式。

以下步骤可用于部署`eosio.msig`合同。

```
$ cleos create account eosio eosio.msig EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ
executed transaction: ccf74668c29d9c581aa64692f9d24df43d0cdba40366bd1c33a598b2feb198d4  352 bytes  102400 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"eosio.msig","owner":{"threshold":1,"keys":[{"key":"EOS5zgUiXJxDNvP8HJRrzX...
```

# 8.部署 Hello World 合约

我们现在将创建我们的第一个“hello world”合同。创建一个名为“hello”的新文件夹，将 cd 放入该文件夹，然后创建一个包含以下内容的文件“hello.cpp ”:

**hello/hello.cpp**

```
#include <eosiolib/eosio.hpp>
#include <eosiolib/print.hpp>
using namespace eosio;
​
class hello : public eosio::contract {
  public:
      using contract::contract;
​
      /// @abi action 
      void hi( account_name user ) {
         print( "Hello, ", name{user} );
      }
};
​
EOSIO_ABI( hello, (hi) )
```

您可以将代码编译成 web 程序集(。wast)如下:

```
$ eosiocpp -o hello.wast hello.cpp
```

现在生成 abi:

```
$ eosiocpp -g hello.abi hello.cpp
Generated hello.abi
```

创建帐户并上传合同:

```
$ cleos create account eosio hello.code EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ EOS5zgUiXJxDNvP8HJRrzXdSEdnL3S4MW8c86257HAMyqmmz2dGsZ
...
$ cleos set contract hello.code ../hello -p hello.code
...
```

现在我们可以运行合同了:

```
$ cleos push action hello.code hi '["user"]' -p user
executed transaction: 2b4691674e206f69fd68ea458ea152405d436ba19ae06af5a697d38bfbbca2e4  232 bytes  102400 cycles
#    hello.code <= hello.code::hi               {"user":"user"}
>> Hello, user
```

这时契约允许任何人授权它，我们也可以说:

```
$ cleos push action hello.code hi '["tester"]' -p tester
executed transaction: c9b765e80f3b31b8946f18f59ed3f70495af769acb4923799e3bf647ee1df8d5  232 bytes  102400 cycles
#    hello.code <= hello.code::hi               {"user":"tester"}
>> Hello, tester
```

[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)