## 主网版本1.0.2.2本地安装
#### 古千峰@BTCMedia

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

六、运行自动化编译脚本：`./eosio_build.sh`，对代码进行编译

```
./eosio_build.sh
```
自动化编译过程可能会因为主机物理内存小于7G，boost库，mongodb等下载失败或其他原因而编译失败，请查找原因或修改脚本自行解决。

七、进到`build`目录中，执行
```
make install
```
否则cleos不会同步到新的版本。

八、在config.ini文件中添加以下内容
*非常重要*
```
producer-name = eosio #默认这里被注释了，也就是默认节点不能产生区块，必须配置区块生产者。注意：producer-name只能设为eosio
enable-stale-production = true #默认为false，要让节点产生区块，设为true

# Plugins used for full nodes
plugin = eosio::wallet_api_plugin
plugin = eosio::chain_api_plugin
plugin = eosio::history_api_plugin
plugin = eosio::chain_plugin
plugin = eosio::history_plugin
plugin = eosio::net_plugin
plugin = eosio::net_api_plugin
```

九、config.ini和genesis.json文件放置路径

* linux-ubuntu:  ~/.local/share/eosio/nodeos/config/
* mac-osx:  ~/Library/Application\ Support/eosio/nodeos/data/config

十、启动nodeos前注意：清理旧的数据（如果之前有老版本的话）。
进到目录中：
* linux-ubuntu:  ~/.local/share/eosio/nodeos/
* mac-osx:  ~/Library/Application\ Support/eosio/nodeos/data
执行以下命令，删除所有旧的data数据：
```
rm -rf data
```
因为配置文件中已经添加了plugin，只需要执行`nodeos`即可

```
nodeos
```

十一、使用`nodeos -v` 查看 `nodeos`的版本，1.0.2.2版本位 `3679913985`

```
nodeos -v 
```
