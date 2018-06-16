## EOS nodeos配置说明
##### 翻译：古千峰@BTCMedia

`nodeos`命令在`~/eos/build/programs/nodeos`路径下，同时在安装时，该可执行文件已经被复制到`/usr/local/bin`下。

`nodeos`可以在启动时，通过命令行配置，也可以通过`config.ini`文件配置。

每个命令行的配置参数，都对应着`config.ini`配置文件中的同名参数。
如：`--plugin eosio::chain_api_plugin` 对应着`config.ini`文件中的 `plugin = eosio::chain_api_plugin`。

通过`$ nodeos --config path/to/config.ini` 可以指定特定路径下的配置文件。

默认情况下，配置文件在：

* Mac OS: ~/Library/Application Support/eosio/nodeos/config
* Linux: ~/.local/share/eosio/nodeos/config

通过`$ nodeos --data-dir path/to/` 可以设定区块数据的存储路径。
默认的区块数据保存在：

* Mac OS: ~/Library/Application Support/eosio/nodeos/data
* Linux: ~/.local/share/eosio/nodeos/data


#### 默认的配置文件`config.ini`内容：
```
# Track only transactions whose scopes involve the listed accounts. Default is to track all transactions. (eosio::account_history_plugin)
# filter_on_accounts = 

# Limits the maximum time (in milliseconds) processing a single get_transactions call. (eosio::account_history_plugin)
get-transactions-time-limit = 3

# File to read Genesis State from (eosio::chain_plugin)
genesis-json = "genesis.json"

# override the initial timestamp in the Genesis State file (eosio::chain_plugin)
# genesis-timestamp = 

# the location of the block log (absolute path or relative to application data dir) (eosio::chain_plugin)
block-log-dir = "blocks"

# Pairs of [BLOCK_NUM,BLOCK_ID] that should be enforced as checkpoints. (eosio::chain_plugin)
# checkpoint = 

# Limits the maximum time (in milliseconds) that a reversible block is allowed to run before being considered invalid (eosio::chain_plugin)
max-reversible-block-time = -1

# Limits the maximum time (in milliseconds) that is allowed a pushed transaction's code to execute before being considered invalid (eosio::chain_plugin)
max-pending-transaction-time = -1

# Limits the maximum time (in milliseconds) that is allowed a to push deferred transactions at the start of a block (eosio::chain_plugin)
max-deferred-transaction-time = 20

# Override default WASM runtime (eosio::chain_plugin)
# wasm-runtime = 

# Time to wait, in milliseconds, between creating next faucet created account. (eosio::faucet_testnet_plugin)
faucet-create-interval-ms = 1000

# Name to use as creator for faucet created accounts. (eosio::faucet_testnet_plugin)
faucet-name = faucet

# [public key, WIF private key] for signing for faucet creator account (eosio::faucet_testnet_plugin)
faucet-private-key = ["EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]

# The local IP and port to listen for incoming http connections. (eosio::http_plugin)
http-server-address = 127.0.0.1:8888

# Specify the Access-Control-Allow-Origin to be returned on each request. (eosio::http_plugin)
# access-control-allow-origin = 

# Specify the Access-Control-Allow-Headers to be returned on each request. (eosio::http_plugin)
# access-control-allow-headers = 

# Specify if Access-Control-Allow-Credentials: true should be returned on each request. (eosio::http_plugin)
access-control-allow-credentials = false

# The queue size between nodeos and MongoDB plugin thread. (eosio::mongo_db_plugin)
mongodb-queue-size = 256

# MongoDB URI connection string, see: https://docs.mongodb.com/master/reference/connection-string/. If not specified then plugin is disabled. Default database 'EOS' is used if not specified in URI. (eosio::mongo_db_plugin)
# mongodb-uri = 

# The actual host:port used to listen for incoming p2p connections. (eosio::net_plugin)
p2p-listen-endpoint = 0.0.0.0:9876

# An externally accessible host:port for identifying this node. Defaults to p2p-listen-endpoint. (eosio::net_plugin)
# p2p-server-address = 

# The public endpoint of a peer node to connect to. Use multiple p2p-peer-address options as needed to compose a network. (eosio::net_plugin)
# p2p-peer-address = 

# The name supplied to identify this node amongst the peers. (eosio::net_plugin)
agent-name = "EOS Test Agent"

# Can be 'any' or 'producers' or 'specified' or 'none'. If 'specified', peer-key must be specified at least once. If only 'producers', peer-key is not required. 'producers' and 'specified' may be combined. (eosio::net_plugin)
allowed-connection = any

# Optional public key of peer allowed to connect.  May be used multiple times. (eosio::net_plugin)
# peer-key = 

# Tuple of [PublicKey, WIF private key] (may specify multiple times) (eosio::net_plugin)
# peer-private-key = 

# Log level: one of 'all', 'debug', 'info', 'warn', 'error', or 'off' (eosio::net_plugin)
log-level-net-plugin = info

# Maximum number of clients from which connections are accepted, use 0 for no limit (eosio::net_plugin)
max-clients = 25

# number of seconds to wait before cleaning up dead connections (eosio::net_plugin)
connection-cleanup-period = 30

# True to require exact match of peer network version. (eosio::net_plugin)
network-version-match = 0

# number of blocks to retrieve in a chunk from any individual peer during synchronization (eosio::net_plugin)
sync-fetch-span = 100

# Enable block production, even if the chain is stale. (eosio::producer_plugin)
enable-stale-production = false

# Percent of producers (0-100) that must be participating in order to produce blocks (eosio::producer_plugin)
required-participation = 33

# ID of producer controlled by this node (e.g. inita; may specify multiple times) (eosio::producer_plugin)
# producer-name = 

# Tuple of [public key, WIF private key] (may specify multiple times) (eosio::producer_plugin)
private-key = ["EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]

# The path of the wallet files (absolute path or relative to application data dir) (eosio::wallet_plugin)
wallet-dir = "."

# Timeout for unlocked wallet in seconds. Wallets will automatically lock after specified number of seconds of inactivity. Activity is defined as any wallet command e.g. list-wallets. (eosio::wallet_plugin)
# unlock-timeout = 

# eosio key that will be imported automatically when a wallet is created. (eosio::wallet_plugin)
# eosio-key = 

# Plugin(s) to enable, may be specified multiple times
# plugin = 
```

#### 命令行参数说明：
```
Application Options:

Config Options for eosio::account_history_plugin:
  -f [ --filter_on_accounts ] arg       Track only transactions whose scopes 
                                        involve the listed accounts. Default is
                                        to track all transactions.
  --get-transactions-time-limit arg (=3)
                                        Limits the maximum time (in 
                                        milliseconds) processing a single 
                                        get_transactions call.

Config Options for eosio::chain_plugin:
  --genesis-json arg (="genesis.json")  File to read Genesis State from
  --genesis-timestamp arg               override the initial timestamp in the 
                                        Genesis State file
  --block-log-dir arg (="blocks")       the location of the block log (absolute
                                        path or relative to application data 
                                        dir)
  -c [ --checkpoint ] arg               Pairs of [BLOCK_NUM,BLOCK_ID] that 
                                        should be enforced as checkpoints.
  --max-reversible-block-time arg (=-1) Limits the maximum time (in 
                                        milliseconds) that a reversible block 
                                        is allowed to run before being 
                                        considered invalid
  --max-pending-transaction-time arg (=-1)
                                        Limits the maximum time (in 
                                        milliseconds) that is allowed a pushed 
                                        transaction's code to execute before 
                                        being considered invalid
  --max-deferred-transaction-time arg (=20)
                                        Limits the maximum time (in 
                                        milliseconds) that is allowed a to push
                                        deferred transactions at the start of a
                                        block
  --wasm-runtime wavm/binaryen          Override default WASM runtime
  --shared-memory-size-mb arg (=32768)  Maximum size MB of database shared 
                                        memory file
  --contracts-console                   Print contract's output to console

Command Line Options for eosio::chain_plugin:
  --replay-blockchain                   clear chain database and replay all 
                                        blocks
  --resync-blockchain                   clear chain database and block log
  --skip-transaction-signatures         Disable transaction signature 
                                        verification. ONLY for TESTING.

Config Options for eosio::faucet_testnet_plugin:
  --faucet-create-interval-ms arg (=1000)
                                        Time to wait, in milliseconds, between 
                                        creating next faucet created account.
  --faucet-name arg (=faucet)           Name to use as creator for faucet 
                                        created accounts.
  --faucet-private-key arg (=["EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"])
                                        [public key, WIF private key] for 
                                        signing for faucet creator account

Config Options for eosio::http_plugin:
  --http-server-address arg (=127.0.0.1:8888)
                                        The local IP and port to listen for 
                                        incoming http connections; set blank to
                                        disable.
  --https-server-address arg            The local IP and port to listen for 
                                        incoming https connections; leave blank
                                        to disable.
  --https-certificate-chain-file arg    Filename with the certificate chain to 
                                        present on https connections. PEM 
                                        format. Required for https.
  --https-private-key-file arg          Filename with https private key in PEM 
                                        format. Required for https
  --access-control-allow-origin arg     Specify the Access-Control-Allow-Origin
                                        to be returned on each request.
  --access-control-allow-headers arg    Specify the Access-Control-Allow-Header
                                        s to be returned on each request.
  --access-control-allow-credentials    Specify if Access-Control-Allow-Credent
                                        ials: true should be returned on each 
                                        request.

Config Options for eosio::mongo_db_plugin:
  -q [ --mongodb-queue-size ] arg (=256)
                                        The queue size between nodeos and 
                                        MongoDB plugin thread.
  -m [ --mongodb-uri ] arg              MongoDB URI connection string, see: 
                                        https://docs.mongodb.com/master/referen
                                        ce/connection-string/. If not specified
                                        then plugin is disabled. Default 
                                        database 'EOS' is used if not specified
                                        in URI.

Config Options for eosio::net_plugin:
  --p2p-listen-endpoint arg (=0.0.0.0:9876)
                                        The actual host:port used to listen for
                                        incoming p2p connections.
  --p2p-server-address arg              An externally accessible host:port for 
                                        identifying this node. Defaults to 
                                        p2p-listen-endpoint.
  --p2p-peer-address arg                The public endpoint of a peer node to 
                                        connect to. Use multiple 
                                        p2p-peer-address options as needed to 
                                        compose a network.
  --agent-name arg (="EOS Test Agent")  The name supplied to identify this node
                                        amongst the peers.
  --allowed-connection arg (=any)       Can be 'any' or 'producers' or 
                                        'specified' or 'none'. If 'specified', 
                                        peer-key must be specified at least 
                                        once. If only 'producers', peer-key is 
                                        not required. 'producers' and 
                                        'specified' may be combined.
  --peer-key arg                        Optional public key of peer allowed to 
                                        connect.  May be used multiple times.
  --peer-private-key arg                Tuple of [PublicKey, WIF private key] 
                                        (may specify multiple times)
  --max-clients arg (=25)               Maximum number of clients from which 
                                        connections are accepted, use 0 for no 
                                        limit
  --connection-cleanup-period arg (=30) number of seconds to wait before 
                                        cleaning up dead connections
  --network-version-match arg (=0)      True to require exact match of peer 
                                        network version.
  --sync-fetch-span arg (=100)          number of blocks to retrieve in a chunk
                                        from any individual peer during 
                                        synchronization
  --max-implicit-request arg (=1500)    maximum sizes of transaction or block 
                                        messages that are sent without first 
                                        sending a notice

Config Options for eosio::producer_plugin:

  -e [ --enable-stale-production ]      Enable block production, even if the 
                                        chain is stale.
  --required-participation arg (=33)    Percent of producers (0-100) that must 
                                        be participating in order to produce 
                                        blocks
  -p [ --producer-name ] arg            ID of producer controlled by this node 
                                        (e.g. inita; may specify multiple 
                                        times)
  --private-key arg (=["EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"])
                                        Tuple of [public key, WIF private key] 
                                        (may specify multiple times)

Config Options for eosio::txn_test_gen_plugin:
  --txn-reference-block-lag arg (=0)    Lag in number of blocks from the head 
                                        block when selecting the reference 
                                        block for transactions (-1 means Last 
                                        Irreversible Block)

Config Options for eosio::wallet_plugin:
  --wallet-dir arg (=".")               The path of the wallet files (absolute 
                                        path or relative to application data 
                                        dir)
  --unlock-timeout arg                  Timeout for unlocked wallet in seconds.
                                        Wallets will automatically lock after 
                                        specified number of seconds of 
                                        inactivity. Activity is defined as any 
                                        wallet command e.g. list-wallets.
  --eosio-key arg                       eosio key that will be imported 
                                        automatically when a wallet is created.

Application Config Options:
  --plugin arg                          Plugin(s) to enable, may be specified 
                                        multiple times

Application Command Line Options:
  -h [ --help ]                         Print this help message and exit.
  -v [ --version ]                      Print version information.
  --print-default-config                Print default configuration template
  -d [ --data-dir ] arg                 Directory containing program runtime 
                                        data
  --config-dir arg                      Directory containing configuration 
                                        files such as config.ini
  -c [ --config ] arg (=config.ini)     Configuration file name relative to 
                                        config-dir
  -l [ --logconf ] arg (=logging.json)  Logging configuration file name/path 
                                        for library users
```