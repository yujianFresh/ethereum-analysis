### cmd包下的geth子包主函数启动的各个子命令解析

/geth

* init
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

* import
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

* export
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


* copydb

* removedb

* dump

* monitor

* account

* wallet

* console

* attach

* js

* makecache

* makedag

* version

* bug

* license

* dumpconfig

