## EOSIO编译安装
##### 翻译：古千峰@BTCMedia

##### *以下安装基于Dawn3.0版本，目前主网版本位1.0.2.2，参见《主网版本1.0.2.2本地安装》*

#### 第一步：下载github上的代码
```
git clone https://github.com/EOSIO/eos --recursive
```
#### 第二步：运行安装程序
```
cd eos
./eosio_build.sh
```

#### 第三步：验证安装是否成功
首先，运行mongod，在Linux系统中，运行：
```
~/opt/mongodb/bin/mongod -f ~/opt/mongodb/mongod.conf &
```
在Mac系统中，运行：
```
/usr/local/bin/mongod -f /usr/local/etc/mongod.conf &
```
然后，运行以下命令，用于检验eosio是否安装成功：
```
cd build
make test
```

#### 注意：
##### 1- EOSIO的安装最低配置是

* 7GB RAM free required
* 20GB Disk free required

如果电脑配置不满足，会提示资源不足。
请进到`eos/scripts/`路径，修改对应的sh文件，找到`Your system must have 7 or more Gigabytes of physical memory installed`字样，或者`You must have at least %sGB of available storage to install EOSIO`，将后面的`exit 1`命令去掉，或者将判断语句全部注释掉，即可。

##### 2- 编译时间在20-30分钟
为了编译过程顺畅，建议使用海外服务器。如：[DigitalOcean](https://www.digitalocean.com/)

##### 3- 目前eosio支持的操作系统
```
Amazon 2017.09 and higher.
Centos 7.
Fedora 25 and higher (Fedora 27 recommended).
Mint 18.
Ubuntu 16.04 (Ubuntu 16.10 recommended).
MacOS Darwin 10.12 and higher (MacOS 10.13.x recommended).
```