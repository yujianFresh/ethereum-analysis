### cmd包下的geth子包主函数启动的各个子命令解析

#### /geth

* __init__
```
  function:导入创世块的json,以指定的json作为创世块
  args:<genesisPath>
  demo: init /home/yujian/eth-go/genesis.json
  notice:ethereum默认创建路径为home目录的.ethereum,如果该目录下已经写入过创世快，会执行失败
  success_result_demo:
    WARN [01-16|10:25:12] No etherbase set and no accounts found as default
    INFO [01-16|10:25:12] Allocated cache and file handles         database=/home/yujian/.ethereum/geth/chaindata cache=16 handles=16
    INFO [01-16|10:25:12] Writing custom genesis block
    INFO [01-16|10:25:12] Successfully wrote genesis state         database=chaindata                             hash=bf2891…ad1419
    INFO [01-16|10:25:12] Allocated cache and file handles         database=/home/yujian/.ethereum/geth/lightchaindata cache=16 handles=16
    INFO [01-16|10:25:12] Writing custom genesis block
    INFO [01-16|10:25:12] Successfully wrote genesis state         database=lightchaindata                             hash=bf2891…ad1419
```

* __import__
```
  function:导入区块数据到当前运行的链中
  args:<filename> (<filename 2> ... <filename N>)
  demo: import /home/yujian/eth-block.txt
  notice:如果导入的文件只要一个是错误的，那么会失败，如果有多个文件错误，那么程序会继续执行忽略错误的．
  success_result_demo:
    WARN [01-16|11:00:32] No etherbase set and no accounts found as default
    INFO [01-16|11:00:32] Allocated cache and file handles         database=/home/yujian/.ethereum/geth/chaindata cache=128 handles=1024
    Import done in 306.866µs.

    INFO [01-16|11:00:32] Disk storage enabled for ethash caches   dir=/home/yujian/.ethereum/geth/ethash count=3
    Compactions
    INFO [01-16|11:00:32] Disk storage enabled for ethash DAGs     dir=/home/yujian/.ethash               count=2
     Level |   Tables   |    Size(MB)   |    Time(sec)  |    Read(MB)   |   Write(MB)
    -------+------------+---------------+---------------+---------------+---------------
    INFO [01-16|11:00:32] Loaded most recent local header          number=0 hash=bf2891…ad1419 td=33554432
       0   |          3 |       0.00094 |       0.00000 |       0.00000 |       0.00000
    INFO [01-16|11:00:32] Loaded most recent local full block      number=0 hash=bf2891…ad1419 td=33554432

    Trie cache misses:  0
    INFO [01-16|11:00:32] Loaded most recent local fast block      number=0 hash=bf2891…ad1419 td=33554432
    Trie cache unloads: 0

    INFO [01-16|11:00:32] Importing blockchain                     file=/home/yujian/eth-block.txt
    Object memory: 34.677 MB current, 34.670 MB peak
    System memory: 74.100 MB current, 73.850 MB peak
    Allocations:   0.015 million
    GC pause:      10.454997ms

    Compacting entire database...
    INFO [01-16|11:00:33] Database closed                          database=/home/yujian/.ethereum/geth/chaindata
    Compaction done in 64.422235ms.

    Compactions
     Level |   Tables   |    Size(MB)   |    Time(sec)  |    Read(MB)   |   Write(MB)
    -------+------------+---------------+---------------+---------------+---------------
       0   |          0 |       0.00000 |       0.00451 |       0.00000 |       0.00021
       1   |          1 |       0.00053 |       0.00435 |       0.00114 |       0.00053
```

* __export__
```
  function:导出区块数据到指定的文件
  args:<filename> [<blockNumFirst> <blockNumLast>]
  demo: export /home/yujian/eth-block.txt
  notice:
  success_result_demo:
    WARN [01-16|10:51:36] No etherbase set and no accounts found as default
    INFO [01-16|10:51:36] Allocated cache and file handles         database=/home/yujian/.ethereum/geth/chaindata cache=128 handles=1024
    INFO [01-16|10:51:36] Disk storage enabled for ethash caches   dir=/home/yujian/.ethereum/geth/ethash count=3
    INFO [01-16|10:51:36] Disk storage enabled for ethash DAGs     dir=/home/yujian/.ethash               count=2
    Export done in 628.951µsINFO [01-16|10:51:36] Loaded most recent local header          number=0 hash=bf2891…ad1419 td=33554432
    INFO [01-16|10:51:36] Loaded most recent local full block      number=0 hash=bf2891…ad1419 td=33554432
    INFO [01-16|10:51:36] Loaded most recent local fast block      number=0 hash=bf2891…ad1419 td=33554432
    INFO [01-16|10:51:36] Exporting blockchain                     file=/home/yujian/eth-block.txt
    INFO [01-16|10:51:36] Exporting batch of blocks                count=1
    INFO [01-16|10:51:36] Exported blockchain                      file=/home/yujian/eth-block.txt
```


* __copydb__
```
  function:从存在的链数据文件夹中创建一条本地链
  args:<sourceChaindataDir>
  demo: copydb /home/yujian/eth-block.txt
  notice:
  success_result_demo:
    WARN [01-16|11:35:55] No etherbase set and no accounts found as default
    INFO [01-16|11:35:55] Allocated cache and file handles         database=/home/yujian/.ethereum/geth/chaindata cache=128 handles=1024
    INFO [01-16|11:35:55] Disk storage enabled for ethash caches   dir=/home/yujian/.ethereum/geth/ethash count=3
    INFO [01-16|11:35:55] Disk storage enabled for ethash DAGs     dir=/home/yujian/.ethash               count=2
    INFO [01-16|11:35:55] Loaded most recent local header          number=0 hash=bf2891…ad1419 td=33554432
    INFO [01-16|11:35:55] Loaded most recent local full block      number=0 hash=bf2891…ad1419 td=33554432
    INFO [01-16|11:35:55] Loaded most recent local fast block      number=0 hash=bf2891…ad1419 td=33554432
    INFO [01-16|11:35:55] Allocated cache and file handles         database=/home/yujian/eth-go/geth/chaindata    cache=128 handles=256
    INFO [01-16|11:35:55] Block synchronisation started
    INFO [01-16|11:35:55] Imported new state entries               count=1 elapsed=33.277µs processed=1 pending=0 retry=0 duplicate=0 unexpected=0
    INFO [01-16|11:35:56] Imported new block headers               count=11 elapsed=1.276s   number=11 hash=ec3374…59a2ba ignored=0
    Database copy done in 1.280940454s
    Compacting entire database...
    INFO [01-16|11:35:56] Imported new chain segment               blocks=11 txs=0 mgas=0.000 elapsed=1.961ms  mgasps=0.000 number=11 hash=ec3374…59a2ba
    Compaction done in 11.130389ms.
```

* __removedb__
```
   function:移除当前链的数据库
   args:""
   demo: removedb
   notice:默认当前链的数据在home目录下的.ethereum目录下
   success_result_demo:
     WARN [01-16|11:48:09] No etherbase set and no accounts found as default
     /home/yujian/.ethereum/geth/chaindata
     Remove this database? [y/N] y
     /home/yujian/.ethereum/geth/lightchaindata
     INFO [01-16|11:48:19] Database successfully deleted            database=chaindata elapsed=7.832ms
     Remove this database? [y/N] y
     INFO [01-16|11:48:27] Database successfully deleted            database=lightchaindata elapsed=481.229µs
```


* __dump__
```
   function:转存指定的块
   args:[<blockHash> | <blockNum>]...
   demo: dump 0
   notice:
   success_result_demo:
     "storage": {}
             },
             "af3cb5965933e7dad883693b9c3e15beb68a4873": {
                 "balance": "2000000000000000000000",
                 "nonce": 0,
                 "root": "56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
                 "codeHash": "c5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470",
                 "code": "",
                 "storage": {}
             },
             "af4493e8521ca89d95f5267c1ab63f9f45411e1b": {
                 "balance": "200000000000000000000",
                 "nonce": 0,
                 "root": "56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
                 "codeHash": "c5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470",
                 "code": "",
                 "storage": {}
             },
           }
         }
         INFO [01-16|14:02:06] Database closed                          database=/home/yujian/.ethereum/geth/chaindata
```


* __monitor__
```
    function:监视和可视化节点指标
    args:""
    demo: monitor
    notice:该命令本地未调通,需要先启链，才能调用该命令去看监控的状态
    success_result_demo:
    // TODO



```


* __account__
```
    function:管理账户
    args:"[new|list|update|import]"
    demo: account new
    notice:“帐户导入”选项只能用于导入通用密钥文件
    success_result_demo:
      WARN [01-16|14:44:44] No etherbase set and no accounts found as default
      Your new account is locked with a password. Please give a password. Do not forget this password.
      !! Unsupported terminal, password will be echoed.
      Passphrase: 123456

      Repeat passphrase: 123456

      Address: {2a23e9223d7af9c56f5abd0c8226021b51c98cbc}

      Process finished with exit code 0
```


* __wallet__
```
    function:导入预付款钱包(一般用不到，用的是account功能)
    args:""
    demo: wallet import
    notice:"钱包导入"选项只能用于导入预售钱包
    success_result_demo:

```
　　


* __console__
```
    function:开启一条私有链,并打开一个可视化的js钱包控制台
    args:""
    demo: console
    notice:
    success_result_demo:
      INFO [01-16|15:26:13] Starting peer-to-peer node               instance=Geth/v1.7.3-unstable/linux-amd64/go1.9.2
      INFO [01-16|15:26:13] Allocated cache and file handles         database=/home/yujian/.ethereum/geth/chaindata cache=128 handles=1024
      INFO [01-16|15:26:14] Initialised chain configuration          config="{ChainID: 1 Homestead: 1150000 DAO: 1920000 DAOSupport: true EIP150: 2463000 EIP155: 2675000 EIP158: 2675000 Byzantium: 4370000 Engine: ethash}"
      INFO [01-16|15:26:14] Disk storage enabled for ethash caches   dir=/home/yujian/.ethereum/geth/ethash count=3
      INFO [01-16|15:26:14] Disk storage enabled for ethash DAGs     dir=/home/yujian/.ethash               count=2
      INFO [01-16|15:26:14] Initialising Ethereum protocol           versions="[63 62]" network=1
      INFO [01-16|15:26:14] Loaded most recent local header          number=1127484 hash=ae7ee2…52ae4c td=8985161313979839462
      INFO [01-16|15:26:14] Loaded most recent local full block      number=0       hash=d4e567…cb8fa3 td=17179869184
      INFO [01-16|15:26:14] Loaded most recent local fast block      number=1092806 hash=6e839a…2eb127 td=8395031237882431385
      INFO [01-16|15:26:14] Upgrading chain index                    type=bloombits percentage=80
      INFO [01-16|15:26:14] Loaded local transaction journal         transactions=0 dropped=0
      INFO [01-16|15:26:14] Regenerated local transaction journal    transactions=0 accounts=0
      INFO [01-16|15:26:14] Starting P2P networking
      INFO [01-16|15:26:16] UDP listener up                          self=enode://bef014afb2b63c749d1e3917a25dc9e508cb67e6010965f11695beef50c29f40a5949fc2082487fadb262c30ff2fb626ce86d4a7cf994d025f5dfee7231051e6@[::]:30303
      INFO [01-16|15:26:16] RLPx listener up                         self=enode://bef014afb2b63c749d1e3917a25dc9e508cb67e6010965f11695beef50c29f40a5949fc2082487fadb262c30ff2fb626ce86d4a7cf994d025f5dfee7231051e6@[::]:30303
      INFO [01-16|15:26:16] IPC endpoint opened: /home/yujian/.ethereum/geth.ipc
      Welcome to the Geth JavaScript console!

      instance: Geth/v1.7.3-unstable/linux-amd64/go1.9.2
      coinbase: 0x2a23e9223d7af9c56f5abd0c8226021b51c98cbc
      at block: 0 (Thu, 01 Jan 1970 08:00:00 CST)
       datadir: /home/yujian/.ethereum
       modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0
```



* __attach__
```
    function:打开一个可视化的js钱包控制台(连接到一个指定的链)
    args:
           --jspath loadScript          JavaScript root path for loadScript (default: ".")
           --exec value                 Execute JavaScript statement
           --preload value              Comma separated list of JavaScript files to preload into the console
           --datadir "/root/.ethereum"  Data directory for the databases and keystore
    demo:attach --datadir="/home/yujian/.ethereum/geth.ipc"
    notice:必须启动一条本地链，这样才能连接过去
    success_result_demo:
      Welcome to the Geth JavaScript console!

      instance: Geth/v1.7.3-unstable/linux-amd64/go1.9.2
      coinbase: 0x2a23e9223d7af9c56f5abd0c8226021b51c98cbc
      at block: 0 (Thu, 01 Jan 1970 08:00:00 CST)
       datadir: /home/yujian/.ethereum
       modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0
```

* __js__
```
  　function:执行指定的js文件
    args:<jsfile> [jsfile...]
    demo:js
    notice:
    success_result_demo:



```


* __makecache__

* __makedag__

* __version__

* __bug__

* __license__

* __dumpconfig__


##### 参考资料
  * [eth钱包创建](https://www.myetherwallet.com/#generate-wallet)
  * [eth命令使用解释](http://www.blockcode.org/archives/185)


