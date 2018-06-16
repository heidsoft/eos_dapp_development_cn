## EOSIO总体框架
##### 翻译：古千峰@BTCMedia

EOSIO有多个程序模块组成，经常会用到的有以下三个模块：

* `nodeos` - EOSIO核心模块，用于启动eosio服务，在后台运行，启动时可以添加多种插件plugin。
* `cleos` - 命令行界面钱包工具，见[《EOS命令行界面钱包》](eos_command_line_wallet.md)，位于`eos/build/programs/cleos/cleos`
* `keosd` - 管理钱包的各种组件，默认情况下，`keosd`将随`cleos`一起启动。位于`eos/build/programs/keosd`

下图是上面三个工具的关系：
![](https://files.readme.io/8f31cfd-Basic-EOSIO-System-Architecture.png)

另外，还有智能合约的编译工具`eosiocpp`
