## 记账节点与普通节点
##### 翻译：古千峰@BTCMedia

### 记账节点（Block Producer）
##### 建立账户
```
$ cleos create account 
```

##### 设置私钥
在`config.ini`，或命令行中配置
```
private-key = ["PRODUCER_PUBLIC_KEY","PRODUCER_PRIVATE_KEY"]
```

##### 配置节点列表
在`config.ini`，或命令行中配置
```
# Default p2p port is 9876
p2p-peer-address = 123.456.78.9:9876
```

##### 配置插件plugin
```
plugin = eosio::chain_plugin
plugin = eosio::producer_plugin
```

### 非记账节点（Non-producing node）
不产生区块的节点，用于同步区块，并给客户端应用提供`HTTP-RPC API`服务。