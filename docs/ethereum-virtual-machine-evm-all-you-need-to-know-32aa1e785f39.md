# 以太坊虚拟机(EVM)你需要知道的一切

> 原文：<https://medium.com/coinmonks/ethereum-virtual-machine-evm-all-you-need-to-know-32aa1e785f39?source=collection_archive---------15----------------------->

EVM 是以太坊中智能合约的运行时环境

*   EVM 无权访问网络、文件系统或其他进程
*   智能合同对其他智能合同的访问权限有限

账户(外部和合同)

外部帐户由公钥-私钥对(即人)控制

合同账户由代码和账户共同控制

外部账户地址由公钥确定

合同帐户地址在创建合同时确定

## 处理

是一个信息

*   它包括二进制数据(称为有效载荷)
*   如果目标帐户包含代码，则执行代码并提供有效负载作为输入数据
*   如果目标为空，事务将创建一个新合同

## 气体

*   每笔交易都收取一定数量的汽油费
*   其目的是限制执行事务所需的工作量，同时为这种执行支付费用
*   当 EVM 执行交易时，气体根据一组规则耗尽
*   天然气价格是由交易的创建者设置的值

## 存储、内存和堆栈

EVM 有三个地方可以存储数据

## 储存；储备

*   account 有一个称为 storage 的数据区
*   存储 256 位字到 256 位字
*   无法从协定中枚举
*   相对而言，读取成本较高，初始化和修改成本更高
*   由于上述原因，我们应该将您在持久存储中存储的内容减少到契约运行所需的内容

## 记忆

*   线性的，并且可以在字节级被寻址
*   读取限于 256 位宽，写入可以是 8 位宽或 256 位宽
*   由一个字(256 位)扩展
*   当读取或写入先前未接触的存储字时。在扩建时，支付燃气费用
*   内存越大，成本就越高
*   平方缩放

## 堆

*   EVM 不是一台登记机器，而是一台堆叠机器
*   计算是在称为堆栈的数据区域上执行的
*   它的最大大小是 1024 个元素，包含 256 位
*   对堆栈的访问仅限于顶端
*   将栈顶 16 个元素中的一个复制到栈中，或者将栈顶元素与其下 16 个元素中的一个交换
*   所有其他操作根据操作取最上面的两个或更多，并将结果推送到堆栈上
*   要对数据执行更深入的操作，您可以将数据移动到内存或存储中
*   如果不移除栈顶的元素，就不能访问栈的中间或更深的值

## 指令组

*   保持在最低限度，以避免共识问题或不一致
*   所有指令都在基本数据类型(256 位字或片或内存或其他字节数组)上操作
*   通常的算术位逻辑和比较操作存在于指令集中
*   有条件和无条件跳转都是可能的
*   合同可以访问当前块的相关属性，如编号和时间戳

## 消息呼叫

*   合同可以调用其他合同，或者通过消息调用的方式向非合同账户发送以太网
*   与事务相似，它们有源、目标和负载

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 也阅读

[](/coinmonks/leveraged-token-3f5257808b22) [## 杠杆代币[多头代币]终极指南

### 杠杆化令牌是具有杠杆化风险敞口的 ERC20 令牌，不考虑保证金、要求、管理…

medium.com](/coinmonks/leveraged-token-3f5257808b22) [](https://blog.coincodecap.com/crypto-exchange) [## 最佳加密交易所| 2021 年十大加密货币交易所

### 加密货币交易所的加密交易需要了解市场，这可以帮助你获得利润。之前…

blog.coincodecap.com](https://blog.coincodecap.com/crypto-exchange) [](https://blog.coincodecap.com/best-swap-platforms) [## 2021 年最佳加密交换平台| CoinCodeCap

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/best-swap-platforms)  [## 10 大最佳网上赌场[2021] |赢取免费 BTC | CoinCodeCap

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/best-online-casinos) [](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa) [## 2021 年最佳加密借贷平台| 6 大比特币借贷平台

### 获得比特币和其他加密货币的最佳贷款利率

medium.com](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa) [](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069) [## 2021 年 6 大最佳硬件钱包|顶级加密硬件钱包[更新]

### 最好的加密货币硬件钱包是绝对必要的。我们将在 NGRAVE、Ledger Nano X 和…

medium.com](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069) [](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [## 2021 年最佳免费加密交易机器人

### 2021 年币安、比特币基地、库币和其他密码交易所的最佳密码交易机器人。四进制，位间隙…

medium.com](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) [## 最佳 4 个加密交易信号电报通道

### 这是乏味的找到正确的加密交易信号提供商。因此，在本文中，我们将讨论最好的…

medium.com](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b)