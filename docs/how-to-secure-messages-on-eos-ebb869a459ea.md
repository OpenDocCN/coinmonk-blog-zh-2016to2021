# 如何保护 EOS 上的消息

> 原文：<https://medium.com/coinmonks/how-to-secure-messages-on-eos-ebb869a459ea?source=collection_archive---------3----------------------->

![](img/cbb23d8c74d5349b15f6f1307cce4a73.png)

区块链是一个分布式账本，每个数据块都是公开的，每个人都可以看到。你可能想知道，如果我想加密区块链上的消息，特别是 EOS 上的消息怎么办？

# 区块链上保护消息的工作原理

我们可以加入 [NuCypher](https://www.nucypher.com/) 。NuCypher 通过分散式代理重加密服务，帮助 dApp 开发人员在公共区块链上存储、共享和管理私有数据。

这是它的工作原理。

1.  Alice 拥有敏感数据，她希望能够委派访问权限。
2.  Alice 使用她自己的公钥加密她的数据，并将其存储在云中，或分散存储中。
3.  Alice 将访问权委托给 Bob。数据在存储器中被重置为 Bob 的密钥。
4.  Bob 下载数据并用他的私钥解密。

# 保护 EOS 上的消息

我们将从这样一个场景开始，Alice 和 Bob 都可以访问私钥，Alice 希望将敏感数据发送给 Bob，并利用 [pyUmbral](https://github.com/nucypher/pyUmbral) ，这是 NuCypher 对 [Umbral](https://github.com/nucypher/umbral-doc/blob/master/umbral-doc.pdf) (分割密钥阈值代理重新加密)的参考实现。

我们先在 EOS 上构建一个消息队列智能合约。

它构建了一个名为 **messages、**表，其中包含 uint64_t 消息 id、帐户名称 from、帐户名称 to、字符串密文和字符串封装。msg_id 是主键。

它提供了两个动作:`sendmsg`和`deletemsg`。`sendmsg`需要 account_name from 和 to，msg_id，密文和胶囊。密文是加密的消息，胶囊是 Umbral 中的概念，使用 pyUmbral 生成。`deletemsg`基本上接受一个消息 id，验证给定的`to`帐户名，然后删除记录。

```
class queue: public eosio::contract { public:
        using contract::contract; //@abi action
        void sendmsg( account_name from, account_name to, uint64_t msg_id, const std::string & ciphertext, const std::string & capsule) {
            require_auth( from );
            messages_index messages( _self, _self );
            auto itr = messages.find(msg_id);
            if (itr != messages.end()) {
                std::string error = "msg_id already exists: " + std::to_string(msg_id);
                eosio_assert(false, error.c_str()); }
            messages.emplace( from, [&](auto& msg){
                msg.msg_id = msg_id;
                msg.from = from;
                msg.to = to; 
                msg.ciphertext = ciphertext;
                msg.capsule = capsule;
            }); } // @abi action
        void deletemsg( account_name to, uint64_t msg_id) {
            require_auth( to );
            messages_index messages(_self, _self);
            auto itr = messages.find(msg_id);
            if ( itr == messages.end() ) {
                std::string error = "msg_id does not exist: " + std::to_string(msg_id);
                eosio_assert(false, error.c_str());
            }

            if ( itr->to != to ) {
                std::string error = "Receipient not correct: " + eosio::name{itr->to}.to_string();
                eosio_assert( false, error.c_str());
            }

            messages.erase(itr);
         } private:
        //@abi table messages i64
        struct messages {
            uint64_t msg_id;
            account_name from;
            account_name to;
            std::string ciphertext;
            std::string capsule; uint64_t primary_key() const { return msg_id;}
        };

        typedef eosio::multi_index<N(messages), messages> messages_index;};EOSIO_ABI( queue, (sendmsg)(deletemsg) )
```

编译它:

```
eosiocpp -o queue.wast queue.cpp
eosiocpp -g queue.abi queue.cpp
```

创建一个帐户来上传智能合同:

```
$ cleos create account eosio queue EOS5aEqZf22dfThTR8GGMnD8oFvsyzssQnhawNwPzfPv4fwDWhf7H
executed transaction: a767af2c66857...  200 bytes  3309 us
# eosio <= eosio::newaccount            {"creator":"eosio","name":"queue","owner":{"threshold":1,"keys":[{"key":"EOS5aEqZf22dfThTR8GGMnD8oFv...$ cleos set contract queue ../queue
Reading WAST/WASM from ../queue/queue.wasm...
Using already assembled WASM...
Publishing contract...
executed transaction: 38e94741c...  13824 bytes  9561 us
# eosio <= eosio::setcode               {"account":"queue","vmtype":0,"vmversion":0,"code":"00617ee7e...
# eosio <= eosio::setabi                {"account":"queue","abi":"0e656f73696f3a3a6162692f9640675...
```

创建测试帐户 alice 和 bob:

```
$ cleos create account eosio alice EOS6NU3XEvosgRVEbhrBHrkbYVteW7DDVewhjo9jgiYoSqUZamnZe
executed transaction: f0c42065f6d9fc...  200 bytes  243 us
# eosio <= eosio::newaccount            {"creator":"eosio","name":"alice","owner":{"threshold":1,"keys":[{"key":"EOS6NU3XEvosgRVEbhrBHrkbYVt...$ cleos create account eosio bob EOS7cX17CZ8V7yFobaVejAN7sMG39iiC5BmFk7b1NB1NNYcrEu1Go
executed transaction: 51d45916fa252e...  200 bytes  194 us
# eosio <= eosio::newaccount            {"creator":"eosio","name":"bob","owner":{"threshold":1,"keys":[{"key":"EOS7cX17CZ8V7yFobaVejAN7sMG39...
```

让我们继续构建一个客户端来加密/解密消息，并与智能合约进行交互。

1.  生成私钥并写入文件。

```
from umbral import config, keysconfig.set_default_curve(SECP256K1)
private_key = keys.UmbralPrivateKey.gen_key()
f = open('priv.key', 'wb')
f.write(private_key.to_bytes())
f.close()
```

2.构建解析器

```
def create_parser():
    parser = argparse.ArgumentParser(description = 'messenger')
    parser.add_argument('--private-key-file', type = str, dest = 'private_key_file', required = False, help = 'Path to the private key file.')
    parser.add_argument('--send-msg-id', type = str, dest = 'send_msg_id', required = False, help = 'Send a message, msg_id')
    parser.add_argument('--send-msg-from', type = str, dest = 'send_msg_from', required = False, help = 'Send a message, from which EOS account')
    parser.add_argument('--send-msg-to', type = str, dest = 'send_msg_to', required = False, help = 'Send a message, to which EOS account')
    parser.add_argument('--send-msg', type = str, dest = 'send_msg', required = False, help = 'Message to be sent')
    parser.add_argument('--read-msg-to', type = str, dest = 'read_msg_to', required = False, help = 'Read a message, to which EOS account')
    return parserparser = create_parser()
args = parser.parse_args(argv)
```

3.从 privkey 文件中读取。

```
def read_privkey_file(privkey_file):
    if os.path.exists(privkey_file):
        with open(privkey_file, 'rb') as f:
            try:
                privkey = f.read()
            except Exception as e:
                print("Cannot read: {}".format(e))
                sys.exit(1)
            f.close()
            return privkey
    else:
        print('Cannot find file: {}'.format(privkey_file))
        sys.exit(1)
```

3.加密邮件并发送。

```
def send_msg(private_key_file, msg, send_msg_from, send_msg_to, send_msg_id):
    privkey_bytes = read_privkey_file(private_key_file)
    privkey = keys.UmbralPrivateKey.from_bytes(privkey_bytes)
    pubkey = privkey.get_pubkey() plaintext = msg.encode()
    ciphertext, capsule = pre.encrypt(pubkey, plaintext) data = '{"from":"' + send_msg_from + '", "to":"' + send_msg_to + '", "msg_id":"' + send_msg_id + '", "ciphertext": "' + ciphertext.hex() + '", "capsule": "' + capsule.to_bytes().hex() + '"}'
    subprocess.call(['cleos', 'push', 'action', DEFAULT_ACCOUNT, 'sendmsg', data , '-p', send_msg_from])
```

3.从`messages`表中读取，如果`to`匹配`read_msg_to`，解密一条消息，然后删除。

```
def read_and_delete_msg(private_key_file, read_msg_to):
    privkey_bytes = read_privkey_file(private_key_file)
    privkey = keys.UmbralPrivateKey.from_bytes(privkey_bytes)

    payload = '{"scope":"' + DEFAULT_ACCOUNT + '","code":"' + DEFAULT_ACCOUNT + '","table": "' + DEFAULT_TABLE+ '", "json":"true"}'
    response = requests.request("POST", DEFAULT_URL, data=payload)   

    found = False
    for msg in response.json()['rows']:
        if msg['to'] == read_msg_to:
            ciphertext = msg['ciphertext']
            capsule = msg['capsule']
            msg_id = msg['msg_id']
            found = True
            break if found:        
        capule = pre.Capsule.from_bytes(bytes.fromhex(capsule), privkey.params)
        cleartext = pre.decrypt(
                          ciphertext = bytes.fromhex(ciphertext),
                          capsule = capule,
                          decrypting_key = privkey)
        print('Cleartext: {}'.format(cleartext)) print('Deleting msg_id: {}'.format(msg_id))
        data = '{"to":"' + read_msg_to + '", "msg_id":"' + str(msg_id) + '"}'
        subprocess.call(['cleos', 'push', 'action', DEFAULT_ACCOUNT, 'deletemsg', data , '-p', read_msg_to])
```

4.默认值。

```
DEFAULT_ACCOUNT = 'queue'DEFAULT_TABLE = 'messages'DEFAULT_URL= "http://127.0.0.1:8888/v1/chain/get_table_rows"
```

让我们把它们包起来测试！

```
$ python3 messenger.py --private-key-file priv.key --send-msg-id 1 --send-msg-from alice --send-msg-to bob --send-msg hello,bobexecuted transaction: dfe17144e105c54d192...  392 bytes  648 us
#         queue <= queue::sendmsg               {"from":"alice","to":"bob","msg_id":1,"ciphertext":"8c53a656c9...
```

检查`messages`工作台。

```
$ cleos get table queue queue messages   
{
  "rows": [{
      "msg_id": 1,
      "from": "alice",
      "to": "bob",
      "ciphertext": "8c8aa24196152b53da35d9fbf9e3c4d8e10f6b7153a656c9921cb440cc69a782c2ba0c2cf2",
      "capsule": "028c4e8279c0919bdec4ea98b4251f15a74868d2ea7554ab796230af8f88e62cd9031c07e90c6183152e140c43a370f2c12526b6d62269f8673a35e6a19fc6ff78ac2f3a28dbed868858ec71228644f277c8ecfb691aa41dab863072e6f39c55d294"
    }
  ],
  "more": false
}
```

加密的信息在那里。那我们来读吧。

```
$ python3 messenger.py --private-key-file priv.key --read-msg-to bobCleartext: b'hello,bob'
Deleting msg_id: 1executed transaction: 6cdad7694f3fe6c8...112 bytes  566 us
#         queue <= queue::deletemsg    {"to":"bob","msg_id":1}
```

查一下`messages`表，消息没了。

```
$ cleos get table queue queue messages   
{
  "rows": [],
  "more": false
}
```

因此，我们探索了如何保护 EOS 上的消息。接下来，我们可以处理 Alice 和 Bob 拥有自己的密钥或者发送给多个用户的场景。

## 我们的社交

[网站](https://blockgenic.website/)
[推特](https://twitter.com/blockgenic)
[Youtube](https://www.youtube.com/channel/UCXTaehuAs3UWKnMVnT71MMQ)
[Meetup](https://www.meetup.com/nl-NL/Seattle-EOS/)
[Steem](https://steemit.com/@block21)

*由* [写成*由*](https://www.linkedin.com/in/yannick-slenter/) *为* [*为*](https://blockgenic.website/)