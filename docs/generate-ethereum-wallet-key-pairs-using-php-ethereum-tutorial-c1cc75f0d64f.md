# 使用 PHP 生成以太坊钱包和密钥对——以太坊教程

> 原文：<https://medium.com/coinmonks/generate-ethereum-wallet-key-pairs-using-php-ethereum-tutorial-c1cc75f0d64f?source=collection_archive---------1----------------------->

![](img/b5055ecfc1cbef98bc2f5991e216ef42.png)

这篇文章提供了一个关于如何使用 PHP 7.0++生成 ECDSA 私钥，然后导出到以太坊钱包地址的指南。

> 另请阅读:[最佳硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)

> 您可以找到如下工作实现:

# 先决条件

该代码需要 PHP 7.0++与 OpenSSL 扩展和 PHP Composer。您需要使用 PHP Composer 来安装第三方软件包

```
$ composer install
```

# 生成私钥

以太坊标准是使用 secp256k1 曲线来生成私钥。在我的教程中，我使用 OpenSSL 函数生成 PEM 格式的椭圆曲线私钥，如下所示:

```
$config = [
    'private_key_type' => OPENSSL_KEYTYPE_EC,
    'curve_name' => 'secp256k1'
];$res = openssl_pkey_new($config);
if (!$res) { echo 'ERROR: Fail to generate private key. -> ' . openssl_error_string(); exit;
}// Generate Private Key
openssl_pkey_export($res, $priv_key);// PEM Format
$priv_pem = *PEM*::fromString($priv_key);
```

之后我需要将私钥转换成 ASN1 序列，下面是 ANS1 序列的结构。

```
ECPrivateKey ::= SEQUENCE {
    version        INTEGER { ecPrivkeyVer1(1) } (ecPrivkeyVer1),
    privateKey     OCTET STRING,
    parameters [0] ECParameters {{ NamedCurve }} OPTIONAL,
    publicKey  [1] BIT STRING OPTIONAL
}
```

下面的代码是我如何从 ANS1 序列结构中检索十六进制字符串的公钥和私钥。

```
// Then convert it to ASN1 Structure
$ec_priv_seq = $ec_priv_key->toASN1();// Private Key & Public Key in HEX
$priv_key_hex = bin2hex($ec_priv_seq->at(1)->asOctetString()->string());$priv_key_len = strlen($priv_key_hex) / 2;$pub_key_hex = bin2hex($ec_priv_seq->at(3)->asTagged()->asExplicit()->asBitString()->string());$pub_key_len = strlen($pub_key_hex) / 2;
```

# 导出以太坊钱包地址

以太坊钱包地址来源于公钥。每个 EC 公钥总是以 0x04 开始。为了获得以太坊钱包地址的正确散列，我们需要删除前导 0x04。

```
$pub_key_hex_2 = substr($pub_key_hex, 2);
$pub_key_len_2 = strlen($pub_key_hex_2) / 2;
```

我们继续使用 EC 公钥的 Keccak256 散列。以太坊钱包地址是 20 字节长，即 40 个字符长，所以我们只需要散列数据的最后 20 个字节。

```
$hash = *Keccak*::hash(hex2bin($pub_key_hex_2), 256);
$wallet_address = '0x' . substr($hash, -40);
$wallet_private_key = '0x' . $priv_key_hex;
```

现在你有了 **$wallter_address** ，它存储了你新生成的以太坊钱包地址， **$wallet_private_key** 是你以太坊钱包地址的私钥。

# 如何运行代码

```
$ php GenerateEthereumWallet.php
```

它将输出以太坊钱包地址及其私钥，如下所示:

```
ETH Wallet Address: 0xb2...
Private Key: 0x73...
```

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)