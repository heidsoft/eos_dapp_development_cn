## 本地多节点配置
##### 翻译：古千峰@BTCMedia

本教程将解释如何在一台服务器（电脑）上建多个eos节点。
![](https://files.readme.io/a190a39-Single-Host-Multi-Node-Testnet.png)

##### 第一步：启动一个节点的`keosd`
打开电脑一个终端窗口，输入以下命令：
```
keosd --http-server-address 127.0.0.1:8899
```
正常情况下，会出现以下信息：
```
2493323ms thread-0   wallet_plugin.cpp:39          plugin_initialize    ] initializing wallet plugin
2493323ms thread-0   http_plugin.cpp:141           plugin_initialize    ] host: 127.0.0.1 port: 8899
2493323ms thread-0   http_plugin.cpp:144           plugin_initialize    ] configured http to listen on 127.0.0.1:8899
2493323ms thread-0   http_plugin.cpp:213           plugin_startup       ] start listening for http requests
2493324ms thread-0   wallet_api_plugin.cpp:70      plugin_startup       ] starting wallet_api_plugin
...
```
##### 第二步：在第一个节点上创建钱包
以上此打开的终端窗口不要关闭，另外再启动一个终端窗口。在新的终端窗口，输入：
```
cleos --wallet-url http://localhost:8899  wallet create
```
以上命令将在8899端口的节点上，新建了一个钱包，结果如下：
```
Creating wallet: default
Save password to use in the future to unlock this wallet.
Without password imported keys will not be retrievable.
"新账号的hash"
```
*注意端口号与上面一个一致，都是8899。*

##### 第三步：启动一个记账节点
打开第三个终端，输入以下命令：
```
nodeos --enable-stale-production --producer-name eosio --plugin eosio::chain_api_plugin --plugin eosio::net_api_plugin
```
以上命令用于建立一个特殊的BP（记账节点）

##### 第四步：加载`eosio.bios`合约
要启动第二个节点，首先需要加载`eosio.bios`合约。
回到以上第二步，输入以下命令：
```
cleos --wallet-url http://localhost:8899 set contract eosio build/contracts/eosio.bios
```
##### 第五步：创建一个新的帐号`inita`
首先，生成密钥对
```
cleos create key
```
然后，将私钥导入钱包
```
cleos --wallet-url http://localhost:8899 wallet import 5JgbL2ZnoEAhTudReWH1RnMuQS6DBeLZt4ucV6t8aymVEuYg7sr
imported private key for: EOS6hMjoWRF2L8x9YpeqtUEcsDKAyxSuM1APicxgRU1E3oyV5sDEg
```
最后，创建`inita`帐号
```
cleos --wallet-url http://localhost:8899 create account eosio inita EOS6hMjoWRF2L8x9YpeqtUEcsDKAyxSuM1APicxgRU1E3oyV5sDEg EOS6hMjoWRF2L8x9YpeqtUEcsDKAyxSuM1APicxgRU1E3oyV5sDEg
executed transaction: d1ea511977803d2d88f46deb554f5b6cce355b9cc3174bec0da45fc16fe9d5f3  352 bytes  102400 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"inita","owner":{"threshold":1,"keys":[{"key":"EOS6hMjoWRF2L8x9YpeqtUEcsDK...
```
##### 第六步：启动第二个节点
打开第四个终端窗口，输入命令：
```
nodeos --producer-name inita --plugin eosio::chain_api_plugin --plugin eosio::net_api_plugin --http-server-address 127.0.0.1:8889 --p2p-listen-endpoint 127.0.0.1:9877 --p2p-peer-address 127.0.0.1:9876 --config-dir node2 --data-dir node2 --private-key [\"EOS6hMjoWRF2L8x9YpeqtUEcsDKAyxSuM1APicxgRU1E3oyV5sDEg\",\"5JgbL2ZnoEAhTudReWH1RnMuQS6DBeLZt4ucV6t8aymVEuYg7sr\"]
```
正常的话，应该可以看到如下内容：
```
2393147ms thread-0   producer_plugin.cpp:176       plugin_startup       ] producer plugin:  plugin_startup() end
2393157ms thread-0   net_plugin.cpp:1271           start_sync           ] Catching up with chain, our last req is 0, theirs is 8249 peer dhcp15.ociweb.com:9876 - 295f5fd
2393158ms thread-0   chain_controller.cpp:1402     validate_block_heade ] head_block_time 2018-03-01T12:00:00.000, next_block 2018-04-05T22:31:08.500, block_interval 500
2393158ms thread-0   chain_controller.cpp:1404     validate_block_heade ] Did not produce block within block_interval 500ms, took 3061868500ms)
2393512ms thread-0   producer_plugin.cpp:241       block_production_loo ] Not producing block because production is disabled until we receive a recent block (see: --enable-stale-production)
2395680ms thread-0   net_plugin.cpp:1385           recv_notice          ] sync_manager got last irreversible block notice
2395680ms thread-0   net_plugin.cpp:1271           start_sync           ] Catching up with chain, our last req is 8248, theirs is 8255 peer dhcp15.ociweb.com:9876 - 295f5fd
2396002ms thread-0   producer_plugin.cpp:226       block_production_loo ] Previous result occurred 5 times
2396002ms thread-0   producer_plugin.cpp:244       block_production_loo ] Not producing block because it isn't my turn, its eosio
```
注意以上命令中的端口。

##### 第七步：同步列表
打开第五个终端，输入以下命令：
```
cleos --wallet-url http://localhost:8899 push action eosio setprods "{ \"version\": 1, \"producers\": [{\"producer_name\": \"inita\",\"block_signing_key\": \"EOS6hMjoWRF2L8x9YpeqtUEcsDKAyxSuM1APicxgRU1E3oyV5sDEg\"}]}" -p eosio@active
executed transaction: 2cff4d96814752aefaf9908a7650e867dab74af02253ae7d34672abb9c58235a  272 bytes  105472 cycles
#         eosio <= eosio::setprods              {"version":1,"producers":[{"producer_name":"inita","block_signing_key":"EOS6hMjoWRF2L8x9YpeqtUEcsDKA...
```

##### 验证
可以通过`get info`完成验证，如通过RPC调用，查看内容是否正确。
```
cleos --url http://localhost:8889 get info
```