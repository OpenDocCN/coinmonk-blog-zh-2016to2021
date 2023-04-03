# 带 web3swift 的以太坊钱包

> 原文：<https://medium.com/coinmonks/getting-started-with-web3swift-1a2454481f70?source=collection_archive---------3----------------------->

这是区块链的关键时刻，因为互联网充满了这种新技术。如今，以太坊区块链比其他区块链发展得更快，已经有许多平台(如 Web、桌面和 Android)在以太坊区块链平台上用 web3j 或 web3.js 实现，但当我们在 iOS 上发现我们得到了非常复杂的实现，不像 web3.js，所以我在 GitHub 上发现了优秀的 lib [web3swift](https://github.com/BANKEX/web3swift) 。

*●这不是以太网官方消息，所以不要混淆。*

# 先决条件

*   [CocoaPods](https://cocoapods.org/) 1.4.0 或以上
*   [XCode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) 9.2 或以上版本

通过在终端中输入以下命令，使用 Homebrew 安装 CocoaPods:

```
$ brew install CocoaPods
```

# 库安装

首先，在您的主目录中创建一个名为 Web3SwiftExample 的 Xcode iOS app 项目。要使用 CocoaPods 将 web3swift 集成到您的 Xcode 项目中，首先创建一个 podfile。

```
$ cd ~/Web3Swift
$ pod init
```

用文本编辑器打开新创建的`Podfile`，并在 pods 部分粘贴以下内容:

```
pod 'web3swift', git: 'https://github.com/BANKEX/web3swift.git'
```

您的 podfile 应该如下所示:

```
# Uncomment the next line to define a global platform for your project
# platform :ios, '9.0'target 'Web3Swift' do
# Comment the next line if you're not using Swift and don't want to use dynamic frameworks
use_frameworks!# Pods for Web3Swift
pod 'web3swift', git: 'https://github.com/BANKEX/web3swift.git'end
```

然后，运行以下命令:

```
$ pod update
$ pod install
```

打开新创建的`.xcworkspace`文件

故事开始了，

# **生成新账户:-**

这个库为我们提供了两种类型密钥库。

1.  *以太密钥存储 V3 :-*

```
do {
 let keystore = try EthereumKeystoreV3.init(password: “Your Password”)
 } catch {
 print(error.localizedDescription)
 }
```

2. *BIP32Keystore(HD Keystore 或 HD wallet):-*

```
let mnemonic = try! BIP39.generateMnemonics(bitsOfEntropy: 256)!let keystore = try! BIP32Keystore(mnemonics: mnemonic, password: password, mnemonicsPassword: “”))
```

*注意:这将生成 24 个单词的助记符。如果你想要 12 个单词的助记符，就用 256 代替 128。*

您还可以将这个密钥库保存在 Key.json 文件中，如下所示:

```
do{let userDir = NSSearchPathForDirectoriesInDomains(.documentDirectory, .userDomainMask, true)[0]let keystoreManager = KeystoreManager.managerForPath(userDir + “/keystore”)var ks: BIP32Keystore?if (keystoreManager?.addresses?.count == 0) {let mnemonic = try! BIP39.generateMnemonics(bitsOfEntropy: 256)!let keystore = try! BIP32Keystore(mnemonics: mnemonic, password: password, mnemonicsPassword: "")) ks = keystore
let keydata = try JSONEncoder().encode(ks?.keystoreParams)FileManager.default.createFile(atPath: userDir + “/keystore”+”/key.json”, contents: keydata, attributes: nil)} else {ks = keystoreManager?.walletForAddress((keystoreManager?.addresses![0])!) as? BIP32Keystore}guard let myaddress= ks?.addresses?.first else {return}print(myaddress.address)}catch{print(error.localizedDescription)}
```

# **在以太中获得平衡:-**

```
let ethAdd = EthereumAddress(address)let balancebigint = web3Rinkeby?.eth.getBalance(address: ethAdd).valueprint("Ether Balance :\(String(describing: Web3.Utils.formatToEthereumUnits(balancebigint ?? 0)!))")
```

*注意:在中可以将 web 3 link by 改为 web3Main 或者自己的同步节点。*

# **以太交易:-**

```
var options = Web3Options.defaultOptions()options.gasLimit = BigUInt(21000)options.from = self.bip32keystore?.addresses?.first!let amountDouble = Int((Double(amount) ?? 0.0)*pow(10, 18))let am = BigUInt.init(amountDouble)options.value = amlet estimatedGasResult = self.web3Rinkeby?.contract(Web3.Utils.coldWalletABI, at: toaddress)!.method(options: options)!.estimateGas(options: nil)guard case .success(let estimatedGas)? = estimatedGasResult else {return}options.gasLimit = estimatedGasvar intermediateSend = self.web3Rinkeby?.contract(Web3.Utils.coldWalletABI, at: toaddress, abiVersion: 2)!.method(options: options)!intermediateSend = self.web3Rinkeby?.contract(Web3.Utils.coldWalletABI, at: toaddress, abiVersion: 2)!.method(options: options)!let sendResult = intermediateSend?.send(password: pass)switch sendResult {case .success(let r)?:print("Sucess",r)case .failure(let err)?:print("Eroor",err)case .none:print("sendResultBip32",sendResult)}
```

***注意:不要在这个 coldwallet 中混淆 ABI 和契约。这不是智能合约的一部分。***

# **使用智能合同和 ABI 文件:-**

**用 ABI 文件创建智能联系人对象**:

```
let contract = web3Rinkeby?.contract(Web3Utils.erc20ABI, at: contractAddress, abiVersion: 2)!
```

你也可以导入你的 abi 文件并解析，但是如果你想创建 ERC20 令牌钱包，这个库包含 ABI 文件，所以不要导入任何类似 ABI 文件的东西。

# **调用智能合约函数:**

## 名称功能:

```
let intermediate = contract?.method("name", parameters:parameters,  options: options)guard let tokenNameRes = intermediate?.call(options: options) else {return}guard case .success(let result) = tokenNameRes else {return}print("token name",result["0"] as! String)
```

**获取智能合约的小数值:**

```
let intermediatedecimal = contract?.method("decimals", parameters:parameters,  options: options)guard let decimal = intermediatedecimal?.call(options: options) else {return}guard case .success(let resultdeci) = decimal else {return}print("result is ",resultdeci["0"] as! BigUInt)
```

## 余额:

```
guard let bkxBalanceResult = contract?.method("balanceOf", parameters: [ethAdd] as [AnyObject], options: options)?.call(options: nil) else {return}guard case .success(let bkxBalance) = bkxBalanceResult, let bal = bkxBalance["0"] as? BigUInt else {return}let tokenBal = (Double(String(bal)) ?? 0.0)/pow(10, decimal value)print("token balance = ",tokenBal)
```

# **智能合约传递函数:**

```
guard case .success(let gasPriceRinkeby)? = self.web3Rinkeby?.eth.getGasPrice() else {return}var tokenTransferOptions = Web3Options.defaultOptions()tokenTransferOptions.gasPrice = gasPriceRinkebytokenTransferOptions.from = self.bip32keystore?.addresses?.firsttokenTransferOptions.to = toaddresslet amoutDouble = (Double(amount) ?? 0.0)*decimalvaluelet amountStr = String(Int(amoutDouble))let amountBigUInt = BigUInt.init(amountStr)!let intermediateForTokenTransfer = self.contract?.method("transfer", parameters: [toaddress, amountBigUInt] as [AnyObject], options: tokenTransferOptions)!let tokenTransferResult = intermediateForTokenTransfer?.send(password: pass, options: tokenTransferOptions, onBlock: "latest")switch tokenTransferResult {case .success(let res)?:print("Token transfer successful",res)print(res)case .failure(let error)?:print(error)case .none:print("something went wrong",tokenTransferResult?.value)}
```

# 使用来自帐户的私钥导出或导入:-

```
do {
//Exporting private key datalet privatekey = try keystoremanager?.UNSAFE_getPrivateKeyData(password: password, account: (keystoremanager?.addresses?.first))print("pkey",privatekey?.toHexString())//Import private key data.
let etheriumKeystore = try EthereumKeystoreV3.init(privateKey: privatekey!, password: newpass)print("etherium address",etheriumKeystore?.addresses?.first?.address)} catch  {print(error.localizedDescription)}
```

*注意:——你也可以使用助记符恢复钱包，探索你的钱包。*

# **最后但同样重要的是:-我是谁？**

我叫 Shivang Pandey，是 [DevGenesis Private Limited](https://www.devgenesis.com/) 的 iOS 应用程序开发人员，目前从事区块链技术和 iOS 应用程序开发工作。

Github 示例项目:-【https://github.com/pandeyshivang/web3swiftSample 

Github 简介:-【https://github.com/pandeyshivang 

领英简介:[https://goo.gl/oam7Ze](https://goo.gl/oam7Ze)

> 还读:[最佳比特币硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)