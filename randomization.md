## 随机数发生器
#### 翻译：古千峰@BTCMedia

（译者）随机数发生器作为一种极其重要的Oracle（预言机），是很多区块链应用必须的一个工具，EOS提供了一种方便的随机数生成方案。

### 游戏目的
本篇通过一个小游戏，双方比较谁的随机数大，来解释如何使用区块链随机数发生器。

### 操作步骤
#### 第一步：产生密钥
在终端执行命令
```
$ openssl rand 32 -hex
$ 28349b1d4bcdc9905e4ef9719019e55743c84efa0c5e9a0b077f0b54fcd84905

$ openssl rand 32 -hex
$ 15fe76d25e124b08feb835f12e00a879bd15666a33786e64b655891fba7d6c12
```

#### 第二步：生成SHA256密钥
```
$ echo -n '28349b1d4bcdc9905e4ef9719019e55743c84efa0c5e9a0b077f0b54fcd84905' | xxd -r -p | sha256sum -b | awk '{print $1}'
$ d533f24d6f28ddcef3f066474f7b8355383e485681ba8e793e037f5cf36e4883

$ echo -n '15fe76d25e124b08feb835f12e00a879bd15666a33786e64b655891fba7d6c12' | xxd -r -p | sha256sum -b | awk '{print $1}'
$ 50ed53fcdaf27f88d51ea4e835b1055efe779bb87e6cfdff47d28c88ffb27129
```

#### 第三步：向智能合约发送密钥
```
$ cleos push action chance submithash '[ "alice", "d533f24d6f28ddcef3f066474f7b8355383e485681ba8e793e037f5cf36e4883" ]' -p alice

$ cleos push action chance submithash '[ "bob", "50ed53fcdaf27f88d51ea4e835b1055efe779bb87e6cfdff47d28c88ffb27129" ]' -p bob
```

#### 第四步：定义数据结构
需要定义两个数据结构，第一个是player
```
struct player {
  checksum256 hash;
  checksum256 secret;
};
```
第二个是game
```
struct game {
	uint64_t id;
  	player player1;
  	player player2;
}
```

#### 第五步：获取随机数，并比较
```
// variable to get result from hashing all players hashes and secrets
checksum256 result;

// hash the contents in memory, starting at game.player1 and spanning for 
// sizeof(player)*2 bytes
sha256( (char *)&game.player1, sizeof(player)*2, &result);

// compares first and second 4 byte chunks in result to determine a winner
int winner = result.hash[1] < result.hash[0] ? 0 : 1;

// report appropriate winner
if( winner ) {
  report_winner(game.player1);
} else {
  report_winner(game.player2);
}
```

注意：sha256是32位的，因此包括8个uint32位随机数。本例子中，获取了前2个作为随机数，当然，也可以获取hash[2]~hash[7]中的随机数。

EOS合约示例中有一个dice合约，下一节介绍dice合约使用。


