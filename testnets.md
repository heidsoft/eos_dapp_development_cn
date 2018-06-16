## EOS Party测试网络
测试区块链浏览器: [https://party.eosmonitor.io/](https://party.eosmonitor.io/)

EOS Party 测试网络是由EOSFans基于[EOS DAWN v1.0.1](https://github.com/EOS-Mainnet/eos)创建的测试网络，该测试网络旨在为开发者提供更好的线上开发和测试环境．

### 加入 EOS Party 测试网络

任何个人,组织都可以加入`EOS Party`, 加入 `EOS Party` 测试网络成为BP需要以下几个步骤：

*   自己的服务器
*   EOS环境搭建(建议使用Docker)
*   创建账号

#### 1- 自己的服务器

作为EOS节点要为网络提供稳定的产生区块，所以需要参与者有稳定的服务器，基础服务器参考配置：   

*   CPU:1核
*   内存:4G
*   磁盘：40G

#### 2- EOS环境搭建(Docker快速部署)

确保你的服务器已经安装`Docker`和`Docker-Compose`, `Docker-Compose`不是必须.

*   `mkdir -p /data/eos` 创建你的EOS节点数据存储的目录, 也可以是其他目录.
*   将 [config.ini](https://github.com/eosfansio/EOS-Party-Testnet/blob/master/config.ini) 拷贝到`/data/eos` 并修改四处地方.
	*   p2p-server-address: 改成你自己的服务器IP和监听的p2p端口, 默认是 IP:9876
    *   agent-name: 改成你自己的标识, 域名或其他.
    *   private-key: 你的密钥对, 建议[创建](https://eosfans.io/tools/generate/)全新的.
    *   producer-name: 节点账户名(12位[12345a-z]字符串).

*   将 [genesis.json](https://github.com/eosfansio/EOS-Party-Testnet/blob/master/genesis.json) 文件拷贝到`/data/eos`目录, 不需要修改.
运行Docker命令：
```
    docker run -d \
        --restart=always \
        --name party \
        -v /data/eos:/opt/eosio/bin/data-dir \
        -v /data/eos/nodeos:/root/.local/share/eosio/nodeos \
        -p 8888:8888 \
        -p 9876:9876 \
        eosfans/eos:launch-1.0.1 nodeosd.sh --delete-all-blocks  --genesis-json /opt/eosio/bin/data-dir/genesis.json
```
启动节点.

用`docker logs -f --tail=100 party`  查看最新100条日志.

#### 3- 创建帐号

创建账号：请到[这里](http://203.195.171.163:8081)

网站入口正在开发.

#### 4- 自助申请成为BP

`docker exec -it party bash` 进入容器

`cleos wallet create`  创建一个默认钱包并保存好密码

`cleos wallet import {Private_Key}` 导入私钥

`cleos  system regproducer {producer-name}  {public key} http://{server_ip}:8888`

#### 5- 给自己投票

注册成为BP之后并不会立即出块, 因为你没有票. 可以在水龙头上尝试给自己转一笔EOS(一次最大1万个).

抵押你的1万个EOS:

`cleos system delegatebw {producer-name} {producer-name} '5000.0000 SYS' '5000.0000 SYS' --transfer`

投票给自己:

`cleos system voteproducer prods {producer-name} {producer-name}`
