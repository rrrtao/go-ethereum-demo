# go-ethereum-demo
私有链搭建操作指南

通过geth来配置以太坊私有链

1、安装HomeBrew

https://brew.sh

2、brew tap ethereum/ethereum

3、brew install ethereum --devel

4、mkdir privatechain

5、cd privatechain

6、vim genesis.json

{
  "config": {
        "chainId": 10,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
  "alloc"      : {},
  "coinbase"   : "0x0000000000000000000000000000000000000000",
  "difficulty" : "0x02000000",
  "extraData"  : "",
  "gasLimit"   : "0x2fefd8",
  "nonce"      : "0x0000000000000042",
  "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "timestamp"  : "0x00"
}

7、mkdir data0

此时目录机构

privatechain  
├── data0  
└── genesis.json

8、执行初始化命令

geth --datadir data0 init genesis.json

上面的命令的主体是geth init，表示初始化区块链，命令可以带有选项和参数，其中--datadir选项后面跟一个目录名，这里为data0，表示指定数据存放目录为data0，genesis.json是init命令的参数

此时目录机构

privatechain  
├── data0  
│   ├── geth  
│   │   └── chaindata  
│   │       ├── 000002.log  
│   │       ├── CURRENT  
│   │       ├── LOCK  
│   │       ├── LOG  
│   │       └── MANIFEST-000003  
│   └── keystore  
└── genesis.json


9、启动私有链节点

geth --datadir data0 --networkid 10 console 2>> log

wangjifadeMacBook-Pro:privatechain wangjifa$ geth --datadir data0 --networkid 10 console 2>>log
Welcome to the Geth JavaScript console!

instance: Geth/v1.7.3-stable/darwin-amd64/go1.9.2
coinbase: 0x2d5238dc81b3ce00b51baae4b00ee7c06323798d
at block: 13 (Tue, 23 Jan 2018 10:37:52 CST)
 datadir: /data/www/ethereum/privatechain/data0
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

> 

> 

10、创建账户

> eth.accounts

[]

> personal.newAccount('123456');

"0x2d5238dc81b3ce00b51baae4b00ee7c06323798d"

> personal.newAccount('123456');

"0x8799dcb8f9881cbae31c068532d2ed34dd197777"

> eth.accounts

["0x2d5238dc81b3ce00b51baae4b00ee7c06323798d", "0x8799dcb8f9881cbae31c068532d2ed34dd197777"]

> user1 = eth.accounts[0];

"0x2d5238dc81b3ce00b51baae4b00ee7c06323798d"

> user2 = eth.accounts[1];

"0x8799dcb8f9881cbae31c068532d2ed34dd197777"

> eth.getBalance(user1);

0

> eth.getBalance(user2);

0

> eth.blockNumber

0

> eth.coinbase

"0x2d5238dc81b3ce00b51baae4b00ee7c06323798d"

11、启动&停止挖矿

miner.start(1)；

miner.stop();

12、查看余额
<pre><code>
> eth.blockNumber
13
> eth.getBalance(user1);
ReferenceError: 'user1' is not defined
    at <anonymous>:1:16
> user1 = eth.accounts[0];
"0x2d5238dc81b3ce00b51baae4b00ee7c06323798d"
> user2 = eth.accounts[1];
"0x8799dcb8f9881cbae31c068532d2ed34dd197777"
> eth.getBalance(user1);
65000000000000000000
> web3.fromWei(eth.getBalance(eth.accounts[0]),'ether')
65
> web3.fromWei(eth.getBalance(user1),'ether')
65
</code></pre>

13、产生交易

> eth.getBalance(user2);

0

 账户user2 为0
 
 可以通过发送一笔交易，从账户1转移5个以太币到账户2；
<pre><code>
> amount = web3.toWei(5,'ether')
"5000000000000000000"
> eth.sendTransaction({from:eth.accounts[0],to:eth.accounts[1],value:amount})
Error: authentication needed: password or unlock
    at web3.js:3143:20 
    at web3.js:6347:15
    at web3.js:5081:36
    at <anonymous>:1:1
</code></pre>

账户每隔一段时间就会被锁住

<pre><code>
> personal.unlockAccount(eth.accounts[0])
Unlock account 0x2d5238dc81b3ce00b51baae4b00ee7c06323798d
Passphrase: 
true
> eth.sendTransaction({from:eth.accounts[0],to:eth.accounts[1],value:amount})
"0x3e1e6901e95540f9cd8eb49bd838974a564e7c6e5870f6d2ebf7a4c0db09fd6c"
> 
</code></pre>

此时交易已经提交到区块链，返回了交易的hash，但还未被处理，这可以通过查看txpool来验证：

<pre><code>
> txpool.status
{
  pending: 1,
  queued: 0
}
> 
</code></pre>
 
其中有一条pending的交易，pending表示已提交但还未被处理的交易。

要使交易被处理，必须要挖矿。这里我们启动挖矿，然后等待挖到一个区块之后就停止挖矿：


 














