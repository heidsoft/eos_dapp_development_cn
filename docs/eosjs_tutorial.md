## eosjs使用说明文档
##### 翻译：古千峰@BTCMedia

### 版本说明：

|Version|[EOSIO/eosjs][1]|[Npm][2]|[EOSIO/eos][3]|[Docker][4]|Node
|-------|-------|--------|-------|-------|-------|
|DAWN-2018-04-23-ALPHA|tag:9.x.x|npm install eosjs@dawn3|tag:DAWN-2018-04-23-ALPHA|eosio/eos:DAWN-2018-04-23-ALPHA|[Docker配置][5]|
|dawn3|tag:8.x.x|npm install eosjs@8|tag:dawn-v3.0.0|eosio/eos:dawn3x|[Docker配置][6]|
|dawn2|branch:dawn2|npm install eosjs|branch:dawn-2.x|eosio/eos:dawn2x|[Docker配置][7]|

### 支持环境
node v8+
npm 6.1.11

### EOSJS
用法：
```
Eos = require('eosjs') // Eos = require('./src')

// eos = Eos.Localnet() // 本地127.0.0.1:8888
eos = Eos.Testnet() // 调用eos.io测试网络 testnet at eos.io

// All API methods print help when called with no-arguments.
eos.getBlock() //获取区块

// Next, your going to need nodeos running on localhost:8888

// If a callback is not provided, a Promise is returned
eos.getBlock(1).then(result => {console.log(result)})

// Parameters can be sequential or an object
eos.getBlock({block_num_or_id: 1}).then(result => console.log(result))

// Callbacks are similar
callback = (err, res) => {err ? console.error(err) : console.log(res)}
eos.getBlock(1, callback)
eos.getBlock({block_num_or_id: 1}, callback)

// Provide an empty object or a callback if an API call has no arguments
eos.getInfo({}).then(result => {console.log(result)})
```

API调用的文档见：
- [chain.json][8]
- [account_history.json][9]
### 链接配置
```
Eos = require('eosjs') // Eos = require('./src')

// Optional configuration..
config = {
  keyProvider: ['PrivateKeys...'], // WIF string or array of keys..
  httpEndpoint: 'http://127.0.0.1:8888',
  mockTransactions: () => 'pass', // or 'fail'
  transactionHeaders: (expireInSeconds, callback) => {
    callback(null/*error*/, headers)
  },
  expireInSeconds: 60,
  broadcast: true,
  debug: false,
  sign: true
}

eos = Eos.Localnet(config)
```

参数说明：

- mockTransactions(可选)
    - pass： 不广播，加装交易工作
    - fail： 不广播，加装交易失败
    - null|undefined： 广播（默认）
    
- transactionHeaders(可选)
手动设置交易记录头，该方法中的callback回调函数每次交易都会被调用。头记录的文档见 [eosjs-api#headers][10]

### 选项
例如：`eos.transfer(params, options)`
```
options = {
  broadcast: true,
  sign: true,
  authorization: null
}
```

- authorization `{array<auth>|auth}` - 该参数用于在多签名情况下，识别签名帐号与权限。用字符串格式，如：`account@permission` 或者使用对象，如：`object<{actor:account,permission}>`
    - 如果不写，则使用默认的授权
    - 如果提供授权账户，则其他账户不能被添加
    - 多个账户按照账户名排序

### 用法
如果使用测试网络，需要在keyProvider配置私钥。
```
Eos = require('eosjs') // Eos = require('./src')

eos = Eos.Localnet({keyProvider: '5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3'})

// Run with no arguments to print usage.
eos.transfer() //显示用法

// Usage with options (options are always optional)
options = {broadcast: false} //不广播此笔交易
eos.transfer({from: 'inita', to: 'initb', quantity: '1 EOS', memo: ''}, options) //从inita发送1个EOS到initb账户

// Object or ordered args may be used.
eos.transfer('inita', 'initb', '2 EOS', 'memo', options)

// A broadcast boolean may be provided as a shortcut for {broadcast: false}
eos.transfer('inita', 'initb', '1 EOS', '', false)
```

API方法的参数见 [eosio_system][11]

【译者注】如获取投票者信息使用 `voter_info`，获取区块节点信息使用 `producer_info`，发布资产使用 `issue`，获取区块链参数使用 `blockchain_parameters`等等

关于签名的高级用法，查看 `keyProvider`，[eosjs-keygen][12] 或者 [unit test][13]

### 简写规则（Shorthand）
在一些特殊情况下，可以使用简写，如资产类Asset和授权类Authority
例如：
- deposit: `'1 eos'` 是 `1.0000 EOS`的简写
- owner: `'EOS6MRy..'` 是 `{threshold: 1, keys: [key: 'EOS6MRy..', weight: 1]}` 的简写
- recover: `inita` 或者 `inita@active` 分别是以下的简写形式：

- `{{threshold: 1, accounts: [..actor: inita, permission: active, weight: 1]}}`
- `inita@other` 将`active`权限改为`other`

```
Eos = require('eosjs') // Eos = require('./src')

initaPrivate = '5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3'
initaPublic = 'EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV'
keyProvider = initaPrivate

eos = Eos.Localnet({keyProvider})

eos.newaccount({
  creator: 'inita',
  name: 'mynewacct',
  owner: initaPublic,
  active: initaPublic,
  recovery: 'inita'
})
```

### 智能合约部署
`setcode`命令会在签名与广播前，将WASM的文本智能合约编译成二进制文件，编译将使用npm的 `binaryen`库，因为默认情况下，因为该库文件很大，没有被包括到`eosjs`中。
安装`binaryen`库：
> npm i binaryen@37.0.0

如果遇到`binaryen`版本与EOS版本发生不兼容，请参考 [problematic][14]
在js源码中导入binaryen：
```
binaryen = require('binaryen');
eos = Eos.Testnet({..., binaryen});
```
一个通过eosjs做代币发行的完整代码：（【译者注：此代码适用于Dawn2.0版本】）
```
Eos = require('eosjs')
let {ecc} = Eos.modules

initaPrivate = '5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3';//inita私钥

currencyPrivate = ecc.seedPrivate('currency');
currencyPublic  = ecc.privateToPublic(currencyPrivate);

keyProvider     = [initaPrivate, currencyPrivate];

binaryen = require('binaryen');//导入编译器

eos = Eos.Localnet({keyProvider, binaryen});

eos.newaccount({
    creator:    'inita',
    name:       'currency',
    owner:      currencyPublic,
    active:     currencyPublic,
    recovery:   'inita'
});

contractDir = `${process.env.HOME}/eosio/dawn3/build/contracts/currency`; //合约所在目录
wast = fs.readFileSync(`${contractDir}/currency.wast`);//读取wast文件，wast文件通过eosiocpp编译
abi  = fs.readFileSync(`${contractDir}/currency.abi`);//读取abi文件

//将智能合约发不到区块链
eos.setcode('currency', 0, 0, wast);
eos.setabi('currency', JSON.parse(abi));

currency = null;
// eos.contract(account<string>, [options], [callback]);
eos.contract('currency').then(contract => currency = contract);//部署合约

// 让inita账户执行该合约，并发行一个代币CUR
currency.issue('inita', '10000.0000 CUR', {authorization: 'currency'});
```

### 原子操作
区块链级别的原子操作实例：
```
Eos = require('eosjs')

keyProvider = [
    '5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3',
    Eos.modules.ecc.seedPrivate('currency')
]; //keyProvider的又一种写法

eos = Eos.Localnet({keyProvider});

//如果任何一个操作失败，所有操作将都失败
eos.transaction(eos => 
    {
        eos.transfer('inita', 'initb', '1 EOS', '');
        eos.transfer('inita', 'initc', '1 EOS', '');
    }
    // [options],
    // [callback]
);

//通过智能合约currency交易资产
eos.transaction('currency', currency => {
    currency.transfer('inita', 'initb', '1 CUR', '');
});

//在一次交易中混合不同资产交易
eos.transaction(['currency', 'eosio'], ({currency, eosio}) => {
    currency.transfer('inita', 'initb', '1 CUR', '');
    eosio.transfer('inita', 'initb', '1 EOS', '');
});

//检查后在发送
eos.contract('currency').then(currency => {
    currency.transaction(cur => {
        cur.transfer('inita', 'initb', '1 CUR', '');
        cur.transfer('initb', 'inita', '1 CUR', '');
    });
    currency.transfer('inita', 'initb', '1 CUR', '');
});
```

### 手动配置发送transacition
优点，灵活度更高
```
Eos = require('eosjs')

eos = Eos.Localnet({keyProvider: '5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3'});

eos.transaction({
    actions: [
        {
            account: 'eosio',
            name:    'transfer',
            authorization: [{
                actor:      'inita',
                permission: 'active'
            }],
            data: {
                from:     'inita',
                to:       'initb',
                quantity: '7 EOS',
                memo:     'testing'
            }
        }
    ]
});
```

## 后续开发更新
`eosjs`与`nodeos`的二进制格式将不断的版本更新，因此，在运行`nodeos`时需要添加参数：`--skip-transaction-signatures`。
另外，注意编译包配置文件 `package.json`中的目录结构指向 `./lib`，如果要测试时，需要从`./src`导入import，如下：
```
Eos = require('./src');
eos = Eos.Localnet(...);
```

除了以上提到的外，`eos`实例还可以提供很多信息。
```
// 'nonce' is a struct but could be any type or struct like: uint8 or transaction
nonce = {value: '..'}
nonceBuffer = eos.fc.toBuffer('nonce', nonce)
assert.deepEqual(nonce, eos.fc.fromBuffer('nonce', nonceBuffer))

// Serialization for a smart-contract's Abi:
eos.contract('currency', (error, c) => currency = c)
issue = {to: 'inita', quantity: '1.0000 CUR', memo: 'memo'}
issueBuffer = currency.fc.toBuffer('issue', issue)
assert.deepEqual(issue, currency.fc.fromBuffer('issue', issueBuffer))
```

### 相关库文件参考
以下这些库已经被无缝集成到了`eosjs`中，也许可以被单独拿出来使用：
> var {api, ecc, json, Fcbuffer, format} = Eos.modules

- format [./format.md][15]
    - 区块链名称验证
    - 资产类名称格式化

- eosjs-api [ [Github][16], [NPM\]][17]
    - 远程访问EOS区块链节点（nodeos）的API
    - 使用此API可以直接访问区块链上只读数据（无需对交易签名）

- eosjs-ecc [ [Github][18], [NPM\]][19]
    - 提供私钥、公钥、签名管理，AES，加密与解密
    - 验证公钥/私钥
    - 使用EOS兼容的checksums加密/解密
    - 计算用于分享的秘密

- json {[api][20], [achema][21]}
    - 定义了区块链的json数据操作API

- eosjs-keygen [ [Github][22], [NPM][23] ] 
    - 私钥与密钥管理

- Fcbuffer [ [Github][24], [NPM][25] ]
    - 二进制数据序列化
    - 客户端对交易二进制码签名
    - 允许客户知道当前签名情况

### 区块链浏览器
获取并安装区块链浏览器，通过以下方式安装EOS测试链的区块链浏览器。
```
git clone https://github.com/EOSIO/eosjs.git
cd eosjs
npm install
npm run build_browser
```
编辑网页文件
```
<script src="eos.js"></script>
<script>
var eos = Eos.Testnet()
//...
</script>
```
    


  [1]: https://github.com/EOSIO/eosjs/blob/master/EOSIO/eosjs
  [2]: https://www.npmjs.com/package/eosjs
  [3]: https://github.com/EOSIO/eosjs/blob/master/EOSIO/eos
  [4]: https://hub.docker.com/r/eosio/eos/
  [5]: https://github.com/EOSIO/eosjs/tree/DAWN-2018-04-23-ALPHA/docker
  [6]: https://github.com/EOSIO/eosjs/tree/master/docker
  [7]: https://t1readonly.eos.io/v1/chain/get_info
  [8]: https://github.com/EOSIO/eosjs-api/blob/master/src/api/v1/chain.json
  [9]: https://github.com/EOSIO/eosjs-api/blob/master/src/api/v1/account_history.json
  [10]: https://github.com/EOSIO/eosjs-api/blob/HEAD/docs/index.md#headers--object
  [11]: https://github.com/EOSIO/eosjs/blob/master/src/schema/eosio_system.json
  [12]: https://github.com/eosio/eosjs-keygen
  [13]: https://github.com/EOSIO/eosjs/blob/master/src/index.test.js
  [14]: https://github.com/EOSIO/eos/issues/2187
  [15]: https://github.com/EOSIO/eosjs/blob/master/docs/format.md
  [16]: https://github.com/eosio/eosjs-api
  [17]: https://www.npmjs.org/package/eosjs-api
  [18]: https://github.com/eosio/eosjs-ecc
  [19]: https://www.npmjs.org/package/eosjs-ecc
  [20]: https://github.com/EOSIO/eosjs-api/blob/master/src/api
  [21]: https://github.com/EOSIO/eosjs/blob/master/src/schema
  [22]: https://github.com/eosio/eosjs-keygen
  [23]: https://www.npmjs.org/package/eosjs-keygen
  [24]: https://github.com/eosio/eosjs-fcbuffer
  [25]: https://www.npmjs.org/package/fcbuffer