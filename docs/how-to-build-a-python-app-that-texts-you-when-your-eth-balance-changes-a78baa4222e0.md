# 如何构建一个 Python 应用程序，当你的 ETH 平衡发生变化时，它会给你发短信

> 原文：<https://medium.com/coinmonks/how-to-build-a-python-app-that-texts-you-when-your-eth-balance-changes-a78baa4222e0?source=collection_archive---------0----------------------->

![](img/d9f6918d1f7441e78417e155c896ca6f.png)

You will be building…*THE ETHEREUM WATCHTOWER*

以太坊很复杂。黑客攻击经常发生。当你的平衡发生变化时，收到一条短信不是很好吗？例如，想象你刚刚在 [OpenSea](https://opensea.io/assets) 上买了一个很酷的新[以太精灵](https://www.etheremon.com/)。以太坊上的交易可能需要一段时间，这会让用户紧张。毕竟，发送到错误地址的 ETH 就永远失去了。dApp 用户界面已经有了很大的进步，但是他们现在感觉不那么安全。让我们使用保护！

![](img/551d5e0cca8bc34763d361ce4803f291.png)

This is what I imagine I’m doing when I interact with dApps

**目标:**我们将构建一个包含不到 150 行代码的 Python 应用程序，它可以在指定的以太坊余额发生变化时发送短信提醒。我选择继续在 Heroku 上部署脚本。

* *为什么是 python？没理由；我也可以很容易地用 Javascript 构建它。我唯一的标准是我的语言必须有一个健壮的 Web3 包。在[上一篇文章中，我写了一个教程](/carbon-money/how-to-build-and-deploy-a-full-stack-upgradeable-erc-20-dapp-81a7e35e374)来构建一个 ReactJS 前端钱包来与以太坊智能合约交互，所以这次我想展示如何构建一个简单的 Python 脚本。

**设置:**

*   [创建一个 Twilio](https://www.twilio.com/docs/sms/quickstart/python) 帐户，这是一个非常简单的以编程方式发送短信的方法。您将需要一个 Twilio SSID 和一个认证密钥。
*   获得一个 [Infura 账号](https://infura.io/)轻松连接以太坊
*   创建一个 Heroku 帐户来服务您的应用程序

**一)Python 脚本:**

首先，我们将使用 [Infura](https://infura.io/) 通过 Web3 轻松连接到以太坊节点。`settings`加载`dotenv`，它应该包含任何全局变量，包括您的 Infura API 密钥。Web3 是 Python 的 web3 模块，基于流行的 javascript 版本。

```
# setupWeb3.py: Export web3 object using infura to connect to mainnet
from web3 import Web3
import settings
import config# Load constants from .env which are now present as system environment variable
INFURA_API_KEY_MAIN = config.INFURA_API_KEY_MAIN# Set provider using Infura node
w3 = Web3(Web3.HTTPProvider("[https://mainnet.infura.io/v3/](https://mainnet.infura.io/v3/)" + INFURA_API_KEY_MAIN))
```

`config`是一个从本地 dotenv 文件或 [Heroku 配置变量](/taqtilebr/managing-herokus-app-environment-variables-d13fd99610b)中检索全局键值对的模块，您可以通过它们的管理 GUI 或命令行来管理它们。一个例子`config.py`可能包括:

```
# config.py: get global variablesimport os## read Heroku config vars
INFURA_API_KEY_MAIN = os.environ.get("INFURA_API_KEY_MAIN")
TWILIO_SID = os.environ.get("TWILIO_SID")
TWILIO_AUTH_TOKEN = os.environ.get("TWILIO_AUTH_TOKEN")## user wallets to monitor# Owner of smart contracts
owner = os.environ.get("OWNER")
# Validator can set permissions on Regulator
validator = os.environ.get("VALIDATOR")
```

接下来，在`whitelist.py`中输入当余额变化时您想要发送的电话号码。FROM 号码是您注册的 Twilio 号码，它将向您发送短信。

```
# whitelist.py: store phone numbers to text# Twilio verified receivers
NICK = '+1XXXXXXXXXX'# Twilio sender
FROM = '+1XXXXXXXXX'
```

既然我们已经设置了配置，让我们编写我们的“文本调度程序”，它利用 Twilio API 向`whitelist.py`中列出的指定用户发送文本:

```
# textdispatcher.py: text whitelisted user(s) using Twilio APIimport os
import whitelist
import config# Constants
text_from = whitelist.FROM
TWILIO_SID = config.TWILIO_SID
TWILIO_AUTH_TOKEN = config.TWILIO_AUTH_TOKEN# Text recipients specified in textTo list
def text(bod, textTo):
 for recipient in textTo:
     from twilio.rest import Client
     client = Client(TWILIO_SID, TWILIO_AUTH_TOKEN)
     message = client.messages.create(body=bod, from_=text_from, to=recipient)
     print('Sent SMS to {}'.format(recipient))
```

最后，让我们使用来自`setupWeb3.py`的`w3`对象来读取我们的 ETH 余额。该文件稍长，但读取余额很容易:

```
from setupWeb3 import w3try:
  balance = w3.fromWei(w3.eth.getBalance(address), 'ether')
 except:
  print('ERROR reading balance') 
```

要完成该应用程序，请使用 textdispatcher 在事件触发时向用户发送短信，在这种情况下，每当我的余额发生变化时，我都希望收到一条短信，我想知道余额是增加了还是减少了:

```
from textdispatcher import textlatestBlock = w3.eth.blockNumber
oldBalance = w3.fromWei(w3.eth.getBalance(address, block_identifier=latestBlock-1), 'ether')
newBalance = w3.fromWei(w3.eth.getBalance(address), 'ether')# psuedo-code follows...
if newBalance > oldBalance:
   text("balance of {} has increased, blocknumber: {}".format(address, latestBlock), peopleToText)
elif newBalance < oldBalance:
   text("balance of {} has decreased, blocknumber: {}".format(address, latestBlock), peopleToText)
else:
    # Insert action to do here if balance has not changed
```

进入脚本将是`__init__.py`，它将运行一个连续的循环，每两秒钟查询一次以太坊余额:

```
from getBalance import textBalanceOwner, textBalanceValidator
import whitelist
import timedef loop_main(poll_interval):
 # Who will receive the alerts
 recipients = [whitelist.NICK, whitelist.GAVIN]
 while True:
  textBalanceOwner(recipients)
  textBalanceValidator(recipients)
  time.sleep(poll_interval)def main():
 loop_main(2)main()
```

瞧吧！现在你可以安心了，你的 ETH 平衡不会在你不知道的情况下改变！

![](img/4eb6d77ec19b50dc26d0ce166b8bd6d7.png)

**B)在 Heroku 上部署 Git:**有几种方法可以在 Heroku 上连续运行应用程序，更多细节请看这篇 [stackoverflow 帖子](https://stackoverflow.com/questions/39139165/running-simple-python-script-continuously-on-heroku)。我决定使用 Heroku 插件“调度程序”每天运行一次我的脚本。这种方法很容易设置，但不可否认地感觉有点粗糙。

1.  创建一个新的应用程序，我将其命名为“eth-watchtower”。`requirements.txt`和`runtime.txt`将指示 Heroku 安装必要的依赖项来运行您的脚本
2.  对于“部署方法”，我选择通过 Github 方便地部署
3.  或者，在选定的分支上启用自动部署。这使得对“生产”分支的代码更改可以通过 Heroku 立即部署。例如，我自动部署我的`master/`分支
4.  进入 Heroku >设置>显示配置变量，为`config.py`中的所有变量添加全局键值对，如您的 Infura API 密钥或 Twilio 认证令牌。`config.py`使用 Heroku 的`os`包从本地运行的 dotenv 或 Heroku 门户读取全局变量
5.  在 Heroku >资源中搜索并安装**调度程序**插件，并将`python __init__.py`作为日常调度任务。更多细节请看 Heroku 调度器文档

**签署**

[完整 Github 回购此处](https://github.com/nicholaspai/ETHwatchtower/tree/master)

你可以在这个简单的应用程序上添加许多功能，我很想听听你的想法！我希望你觉得这篇教程很容易阅读并且很有帮助，如果你有任何问题，请随时联系我。

我目前是 [Carbon](http://carbon.money) 的产品开发人员，我们刚刚在 ramp @ [fiat.carbon.money](http://fiat.carbon.money) 推出了我们的以太坊-菲亚特，我们很乐意合作:)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)