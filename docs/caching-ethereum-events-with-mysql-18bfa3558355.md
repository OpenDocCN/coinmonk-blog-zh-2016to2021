# 用 MySQL 缓存以太坊事件

> 原文：<https://medium.com/coinmonks/caching-ethereum-events-with-mysql-18bfa3558355?source=collection_archive---------0----------------------->

![](img/c7b04b12f0b3fc9795a4cf78c6aa2364.png)

在这篇文章中，我将展示一个简单的缓存以太坊事件的方法。这里我不会描述什么是事件，因为有很多文章涉及这个主题([这里是最完美的一个](https://media.consensys.net/technical-introduction-to-events-and-logs-in-ethereum-a074d65dd61e))。我只想说，通常我们使用事件进行一些链外操作，例如跟踪令牌的传输或检索特定事务的过滤列表，就像一个很好的旧 SQL 查询一样。

假设我们想做一个跟踪一些令牌转移的网站，一种以太扫描。我们肯定需要这样简单的操作，如:

*   获取所有令牌转移
*   获取从特定以太坊地址进行的转账
*   让转账转移到特定的以太坊地址
*   获取高于或低于特定金额的转账
*   在特定时间范围内获得转移

我们现在在 web3 中拥有的是 **getPastEvents** 方法，其用法示例如下

```
let events = await contract.getPastEvents(  
    "Transfer",  
    {  
      filter: {from:'0x0123456789abcdef0123456789abcdef01234567'},  
      fromBlock: 0,  
      toBlock: 'latest'  
    }  
  );
```

这种方法的主要问题是，随着区块链的增长，它可能会很慢，特别是如果你没有运行自己的以太坊节点，而是使用像 Infura 或 MyEtherApi 这样的公共提供商。

接下来——几乎不可能实现一些复杂的查询，因为过滤器对象的功能非常有限。

此外，已经写入区块链的事件不能更改，只能随着时间的推移添加新的记录。这一事实和其他事实使事件成为缓存的完美目标。

# 数据库选择

在这个例子中，我们将使用 MySQL 作为保存事件记录的数据库。MySQL 能够存储原始 JSON，然后使用 JSON 对象的属性编写查询，就像它们是普通的 SQL 列一样。

# 我们应该储存什么？

让我们仔细看看 **getPastEvents** 方法的结果，以了解我们处理的是什么数据。我举了一些币安硬币转移的例子。每个事件对象具有以下结构:

```
{  
"address": "0xB8c77482e45F1F44dE1745F52C74426C631bDD52",  
"blockHash": "0x19e0d4c4cce0ed7c429b627fc6c5cc5c223c2e9218e233ab2b72e64e817cfcc2",  
"blockNumber": 6813922,  
"logIndex": 111,  
"removed": false,  
"transactionHash": "0x32d660785112b084135e0d4d2b53c0d67e851b735eacb486e44e52b7945b857d",  
"transactionIndex": 84,  
"id": "log_5ea90f71",  
"returnValues": {  
"0": "0x6ACe7E0abCF0dA3097Fa7155149dccd51E20EF82",  
"1": "0xAc951701644936aA95C80ED9f358Fa28f8369075",  
"2": "1000553200000000000",  
"from": "0x6ACe7E0abCF0dA3097Fa7155149dccd51E20EF82",  
"to": "0xAc951701644936aA95C80ED9f358Fa28f8369075",  
"value": "1000553200000000000"  
  },  
"event": "Transfer",  
"signature": "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",  
"raw": {  
"data": "0x0000000000000000000000000000000000000000000000000de2add590e16000",  
"topics": [  
"0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",  
"0x0000000000000000000000006ace7e0abcf0da3097fa7155149dccd51e20ef82",  
"0x000000000000000000000000ac951701644936aa95c80ed9f358fa28f8369075 
    ]  
  }  
}
```

如您所见，事件参数存储在 **returnValues** 属性中。 **blockNumber** ， **transactionHash** ， **logIndex** 也可能有用，我稍后会向您展示。

我们的目标是将这些 JSON 对象写入数据库，并实现可以无缝替换标准 web3 的 **getPastEvents** 方法的简单访问方法。

下面是创建**转移**表的 SQL 脚本。

```
CREATE  TABLE  `eth_cache`.`transfer` (  
`id`  INT  NOT  NULL AUTO_INCREMENT,  
`json`  JSON  NOT  NULL,  
`from`  VARCHAR(45) GENERATED  ALWAYS  AS (json_unquote(json_extract(`json`,'$.returnValues.from'))) VIRTUAL,  
`to`  VARCHAR(45) GENERATED  ALWAYS  AS (json_unquote(json_extract(`json`,'$.returnValues.to'))) VIRTUAL,  
`value`  VARCHAR(45) GENERATED  ALWAYS  AS (json_unquote(json_extract(`json`,'$.returnValues.value'))) VIRTUAL,  
`txHash`  VARCHAR(66) GENERATED  ALWAYS  AS (json_unquote(json_extract(`json`,'$.transactionHash'))) VIRTUAL,  
`logIndex`  INT  GENERATED  ALWAYS  AS (json_unquote(json_extract(`json`,'$.logIndex'))),  
PRIMARY KEY (`id`),  
UNIQUE  INDEX  `IDX_UNIQUE` (`txHash`  ASC, `logIndex`  ASC));
```

需要解释一些重要的事情:

1.  **json** 列被创建为 json 类型。这允许我们使用特殊的语法创建自动生成的列
2.  **从**、**到**、**值** —这些是自动生成的列。这个表达式乍一看似乎很复杂，但实际上非常简单。例如，列中的**值等于 **json** 列中存储的对象的 **returnValues.from** 属性。**
3.  **txHash** 和 **logIndex** 。这些属性组合在一起可以识别每个事件对象。我们需要这些来为一行创建唯一的索引，从而防止偶然的事件重复。

可选地，我们也可以添加数据库索引来提高性能。例如，对于**到**列

```
ALTER  TABLE  `eth_cache`.`transfer`  
ADD  INDEX  `IDX_TO` (`to`  ASC);
```

# 履行

# 先决条件

1.  Node.js .我用的是 8.4.0 版本。
2.  Web3 npm 包与区块链进行交互。我们需要特定版本 1.0.0-beta.35。使用最新版本 beta.36 导致在尝试检索某些事件时出现“*返回值无效，是否耗尽汽油*”错误。

```
npm install web3@1.0.0-beta.35 --save
```

3.要在 JavaScript 中使用 MySQL 数据库，我们应该安装 [mysql](https://www.npmjs.com/package/mysql) 包

```
npm install mysql --save
```

4.最后一个——MySQL 服务器。值得一提的是，我们将使用 MySQL 5.7，因为最新的 8.0 版本似乎与 **mysql** 包不兼容(它在尝试连接时给了我一个奇怪的错误 ER_NOT_SUPPORTED_AUTH_MODE)。

# 与 MySQL 交互

在这个例子中，我们将利用连接池进行查询。

```
const mysql = require('mysql');  
let pool = mysql.createPool({  
	connectionLimit: <connection limit>,  
	host: <database server address>,  
	user: <user>,  
	password: <password>,  
	database: <name of your database schema>  
});
```

使用一个混合版本的查询方法会更方便

```
const util = require('util');  
pool.query = util.promisify(pool.query);
```

现在我们可以使用下面的代码将一条记录插入到之前创建的 **transfer** 表中。

```
async  function  writeEvent(event) {  
   try {  
     await pool.query(  
       `Insert into \`transfer\` (\`json\`) VALUES    (\'${JSON.stringify(event)}\')`  
     );  
   } catch(e) {  
     //if it's 'duplicate record' error, do nothing; 
     // otherwise rethrow  
     if(e.code != 'ER_DUP_ENTRY') {  
       throw e;  
     }  
   }  
}
```

这里我们还检查可能的重复行插入。现在我们不想在这种情况下做任何特别的事情，可能我们已经在前面写了那些重复的事件或者类似的东西。所以我们只考虑这类异常的处理。

# 基本缓存功能。

让我们构造一个契约对象来从中检索事件。

```
let contract = new web3.eth.Contract(abi, <contractAddress>);
```

我们可以在 **abi** 参数中只包含**转移**事件接口，如下所示:

```
let abi = [{  
"anonymous": false,  
"inputs": [  
{ "indexed": true, "name": "from", "type": "address" },  
{ "indexed": true, "name": "to", "type": "address" },  
{ "indexed": false, "name": "value", "type": "uint256" }  
],  
"name": "Transfer",  
"type": "event"  
}];
```

这是缓存功能的基本版本。首先，我们获取事件对象，然后将它们逐个写入数据库。

```
async  function  cacheEvents(fromBlock, toBlock) {  
    let events = await contract.getPastEvents(  
    "Transfer",  
    { filter: {}, fromBlock: fromBlock, toBlock: toBlock }  
    );  

    for(let event in events) {  
      await writeEvent(event);  
    }  
}
```

# 常规区块链扫描

现在让我们将其扩展为一个简单的后台脚本，该脚本不断扫描区块链发出的事件。

一些效用函数

```
const timeout = 30;  
function  sleep(milliseconds) {  
    return  new  Promise(resolve => 
        setTimeout(resolve, milliseconds)
    );  
}  

async  function  poll (fn) {  
    await fn();  
    await sleep(timeout*1000);  
    await poll(fn);  
}
```

第一个是简单的 **async/await** 实现 **setTimeout** 。第二个用于 **fn** 的无限周期调用——工人函数。

有了这些辅助功能，我们的背景扫描仪看起来非常简单

```
async  function  scan() {  
    const MaxBlockRange = 500000;  
    let latestCachedBlock = 0; // latest block written to database
    let latestEthBlock = 0; // latest block in blockchain  

    await poll(async () => {  
	try {  
          //get latest block written to the blockchain  
	  latestEthBlock = await web3.eth.getBlockNumber();  

          //divide huge block ranges to smaller chunks, 
          // of say 500000 blocks max  
	  latestEthBlock = Math.min(
            latestEthBlock, 
            latestCachedBlock + MaxBlockRange
          );  

	  //if it is greater than cached block, search for events  
	  if(latestEthBlock > latestCachedBlock) {  
	    await cacheEvents(latestCachedBlock, latestEthBlock);  

	    //if everything is OK, update cached block value  
	    latestCachedBlock = latestEthBlock + 1;  
	  }  
	} catch (e) {  
	  //we might want to add some simple logging here  
	  console.log(e.toString());  
	}  
  });  
}
```

让我解释一下'**latest eth block+1【T1]'的事情。Web3 的 **getPastEvents(fromBlock，toBlock)** 返回在那个**【from，to】**范围内写的事件，包括边框。因此，如果不递增，下一个 **cacheEvents** 调用将再次返回写入 **latestEthBlock** 的事件作为结果的一部分。**

尽管由于实现了唯一的索引，重复的事件不会被插入到数据库中，但是我们仍然不希望做这些额外的工作。

对于一个简单的背景扫描仪来说，这个实现应该足够了。然而，总有改进的余地。我们稍后再回到这个话题。现在，让我们快速看一下我们现在可以利用这些数据做些什么。

# 检索事件

下面是一个从特定地址选择转账的函数示例

```
async  function  selectTransfersFrom(sender) {  
  return  await pool.query(`select json from transfer t where t.from = \'${sender}\'`);  
}
```

我们使用从列生成的**查询数据库。这里最值得注意的部分是函数的结果看起来就像 web3 的 **getPastEvents** 的结果。这使得重构当前代码更加容易。**

# 进一步的改进

事件对象包含许多对您的应用程序可能毫无用处的属性。最好在写入数据库之前删除多余的内容。这样我们就节省了很多空间。

```
async  function  writeEvent(event) {  
    try {  
	delete event.raw;  
	delete event.event;  
	delete event.blockHash;  
	delete event.type;  
	delete event.id;  
	delete event.signature;  

	await pool.query(  
          `Insert into \`transfer\` (\`json\`) VALUES (\'${JSON.stringify(event)}\')`  
	);  
    } catch(e) {  
      // if it's 'duplicate record' error, do nothing, 
      // otherwise rethrow  
      if(e.code != 'ER_DUP_ENTRY') {  
	throw e;  
      }  
  }  
}
```

您可能也注意到了，当前版本的扫描器每次重新启动时都从#0 块开始。当一直扫描到当前块时，它会尝试将重复记录插入到数据库中。我们可以通过在数据库中查询最新的缓存块来消除这些额外的工作。

不从块#0 开始扫描也不错，但至少在部署契约时从块开始扫描。为了简单起见，您可以使用 [etherscan.io](http://etherscan.io/) 来获得这些信息。

```
async function getLatestCachedBlock() {  
  const defaultInitialBlock = <your contract’s deployment block>;  

  let dbResult = await pool.query(  
    'select json_unquote(json_extract(`json`,\'$.blockNumber\')) \  
	as block from transfer order by id desc limit 1'  
    );  

  return dbResult.length > 0 ? 
    parseInt(dbResult[0].block) : 
    defaultInitialBlock;  
}
```

这里我们再次使用 MySQL json 函数来获取事件对象的 **blockNumber** 属性。

然后更换旧的**扫描**功能

```
let latestCachedBlock = 0;
```

有了新的

```
let latestCachedBlock = await getLatestCachedBlock();
```

# 结论

最后，我们创建了一个简单但有效的事件扫描器，它可以不断地将事件缓存到 MySQL 数据库中。如果您有任何问题，请随时联系我，我会尽力回答。

完整的源代码可以在这里找到[https://github.com/olekon/p1_eth_caching](https://github.com/olekon/p1_eth_caching)。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)