## 连接EOS主网
[原文](https://blog.csdn.net/caokun_8341/article/details/80656765)

一、从github克隆主网代码仓库：

```
git clone https://github.com/EOS-Mainnet/eos  
```

二、更新代码仓库子模块，使用递归参数

```
git submodule update --init --recursive  
```

三、git tag命令查看版本标签

```
git tag  
```

四、将本地仓库代码切换到mainnet-1.0.2.2版本

```
git checkout mainnet-1.0.2.2
```

五、git branch 查看本地仓库代码版本是否mainnet-1.0.2.2
```
git branch
```

六、编译代码之前，使用查找替换，把代码中eosio的公钥：`EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV`
替换成后面这个：`EOS7EarnUhcyYqmdnPon8rm7mBCTnBoot6o7fE2WzjvEX2TdggbL3`

七、运行自动化编译脚本：`./eosio_build.sh`，对代码进行编译

```
./eosio_build.sh
```
自动化编译过程可能会因为主机物理内存小于7G，boost库，mongodb等下载失败或其他原因而编译失败，请查找原因或修改脚本自行解决。

八、config.ini文件内容如下
```
# fullnode sample config

blocks-dir = "blocks"

chain-state-db-size-mb = 1024

reversible-blocks-db-size-mb = 340

contracts-console = false

https-client-validate-peers = 1

http-server-address = 0.0.0.0:8888

access-control-allow-credentials = false

p2p-listen-endpoint = 0.0.0.0:9876

p2p-server-address = 0.0.0.0:9876



# List of peers

p2p-peer-address = p2p.one.eosdublin.io:9876

p2p-peer-address = eu-west-nl.eosamsterdam.net:9876

p2p-peer-address = p2p.mainnet.eosgermany.online:9876

p2p-peer-address = 35.197.190.234:19878

p2p-peer-address = p2p.genereos.io:9876

p2p-peer-address = fullnode.eoslaomao.com:443

p2p-peer-address = new.eoshenzhen.io:10034

p2p-peer-address = node1.eosphere.io:9876

p2p-peer-address = p2p.meet.one:9876

p2p-peer-address = bp.eosbeijing.one:8080

p2p-peer-address = peer1.mainnet.helloeos.com.cn:80

p2p-peer-address = p2p-public.hkeos.com:19875

p2p-peer-address = pub1.eostheworld.io:9876

p2p-peer-address = eu1.eosdac.io:49876

p2p-peer-address = peer.eosio.sg:9876

p2p-max-nodes-per-host = 10


agent-name = "kevincaokun1"

# allowed-connection can be set to "specified" to use whitelisting with the "peer-key" option

allowed-connection = any


# peer-private-key is needed if you are whitelisting specific peers with the "peer-key" option

peer-private-key = ["EOS6qTvpRYx35aLonqUkWAMwAf3mFVugYfQCbjV67zw2aoe7Vx7qd", "5JroNC1B4pz9gJzNZeU7tkU6YMtoeWRCr4CJJwKsVXnJhRbKXSC"]


max-clients = 250

connection-cleanup-period = 30

network-version-match = 1

sync-fetch-span = 100

max-implicit-request = 1500

enable-stale-production = false

pause-on-startup = false

max-transaction-time = 10000

max-irreversible-block-age = -1

txn-reference-block-lag = 0

# Plugins used for full nodes

plugin = eosio::chain_api_plugin

plugin = eosio::history_api_plugin

plugin = eosio::chain_plugin

plugin = eosio::history_plugin

plugin = eosio::net_plugin

plugin = eosio::net_api_plugin
```

九、创世json文件genesis.json 内容如下：
```
{
  "initial_timestamp": "2018-06-08T08:08:08.888",
  "initial_key": "EOS7EarnUhcyYqmdnPon8rm7mBCTnBoot6o7fE2WzjvEX2TdggbL3",
  "initial_configuration": {
    "max_block_net_usage": 1048576,
    "target_block_net_usage_pct": 1000,
    "max_transaction_net_usage": 524288,
    "base_per_transaction_net_usage": 12,
    "net_usage_leeway": 500,
    "context_free_discount_net_usage_num": 20,
    "context_free_discount_net_usage_den": 100,
    "max_block_cpu_usage": 200000,
    "target_block_cpu_usage_pct": 1000,
    "max_transaction_cpu_usage": 150000,
    "min_transaction_cpu_usage": 100,
    "max_transaction_lifetime": 3600,
    "deferred_trx_expiration_window": 600,
    "max_transaction_delay": 3888000,
    "max_inline_action_size": 4096,
    "max_inline_action_depth": 4,
    "max_authority_depth": 6
  }
}
```

十、config.ini和genesis.json文件放置路径

* linux-ubuntu:  ~/.local/share/eosio/nodeos/config/
* mac-osx:  ~/Library/Application\ Support/eosio/nodeos/data/config


十一、启动nodeos前注意：清理旧的数据 

十二、使用`nodeos -v` 查看 `nodeos`的版本是否 ：`3679913985`

```
nodeos -v 
```

十三、nodeos启动后使用 `cleos get info` 命令查看链的`chain_id` 是否下面这行代码： `aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906`

十四、注意如果chain_id不对，则不是主网的链，不会从主网同步数据，遇到这种情况请使用下面命令重新启动nodeos，注意路径和你主机上nodeos 所在的路径一致，并且把genesis.json文件拷贝到该目录下面
```
cd ~/eos/build/program/nodeos
./nodeos --genesis-json genesis.json
```

十五、区块数据正常同步以后，可以使用cleos命令行对链条进行一些操作，比如：查看191块的数据

```
cleos get block 191
```
发现这个191区块存在这样一笔交易，eosio 账户给 b1账户转了 10个EOS，并且备注了这样一句话：
```
Never doubt that a small group of thoughtful, committed citizens can change the world; indeed, it's the only thing that ever has - eosacknowledgments.io
```
翻译成中文是：
```
永远不要怀疑一小群有思想、有责任心的公民能改变世界，事实上，这是唯一的事情。
```

*如果想看以上命令的执行结果截图，请参考*
https://blog.csdn.net/caokun_8341/article/details/80656765

