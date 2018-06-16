## 自动充值与提现
#### 翻译：古千峰@BTCMedia

本教程用于交易所开发基于EOSIO的代币的自动充值与提现程序。

### 配置
#### 准备工作：

* 需要有个本地节点，该节点已经链接EOSIO区块链；
* 已经建立了一个帐号：eosio.token；
* 已经部署了：eosio.token 智能合约；
* 已经学完了《关于智能合约》以及《eosio.token，eosio.exchange与eos.msig》课程

本教程使用eosio.token的`transfer`动作来完成充值与提现操作。

注意：请确保`eosio::history_api_plugin` 插件已经运行。

激活Log文件：
默认情况下，log文件记录功能并没有被激活，你需要在启动`nodeos`时，加入参数 --filter-on，产能激活log文件功能。
```
--filter-on tokenxchange:transfer: --filter-on scott:transfer: --filter-on eosio.token:transfer:
```

#### 配置额外账户
交易所称为：tokenxchanger
本教程使用名为`scott`的账户，请参考之前的教程。


#### 存储代币
基于EOS主链，每个交易需要由超过2/3+1个节点的承认，这个将可能有延迟一秒（甚至更多）的情况发生，


#### 启动条件
命令行检测存量代币数量是否足够。
```
$ cleos get currency balance eosio.token scott SYS 900.0000 SYS
```
然后向`scott`发送代币
```
$ cleos transfer scott tokenxchange "1.0000 SYS" 12345
executed transaction: ce32ac1fbc96e74ea9318d5b18769be9d84f704c9c0f0eab23c6ce95e4b9ce49  136 bytes  505 us
#   eosio.token <= eosio.token::transfer        {"from":"scott","to":"tokenxchange","quantity":"1.0000 SYS","memo":"12345"}
#         scott <= eosio.token::transfer        {"from":"scott","to":"tokenxchange","quantity":"1.0000 SYS","memo":"12345"}
#  tokenxchange <= eosio.token::transfer        {"from":"scott","to":"tokenxchange","quantity":"1.0000 SYS","memo":"12345"}
```
（未完）

