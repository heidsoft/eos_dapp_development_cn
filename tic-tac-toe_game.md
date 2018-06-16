## 井字游戏（Tic-tac-toe）智能合约
#### 翻译：古千峰@BTCMedia

很多网络游戏编程的基础课程，会以井字游戏开始。EOS官方教程也不例外，本篇将讲述如何在EOS区块链上做一个去中心化的双人互动井字游戏。
![](images/timg.jpeg)

### 一、设计与准备

#### 玩家
该游戏将采取标准的3X3井字游戏方式。玩家被定义为两个角色：`host`与·`challenger`，Host（庄）首先画。同时，每对玩家最多同时玩两局，一局先下者做庄，一局后下者坐庄。

#### 棋盘与数据结构
Instead of using o and x as in the traditional tic tac toe game. We use 1 to denote movement by host, 2 to denote movement by challenger, and 0 to denote empty cell. Furthermore, we will use one dimensional array to store the board. Hence:
在游戏中，1代表`host`占的格子，2代表`challenger`占的格子，0代表还没被占领的格子。
![](images/screenshot_1528941282022.png)
如上图，我们设定x（叉）为`host`占的格子，用`1`表示；o（圈）为`challenger`占的格子，用`2`表示，则上图的数据结构可以表示为：`[0, 2, 1, 0, 1, 0, 1, 2, 2]`（先横后竖）

#### 动作
User will have the following actions to interact with this contract:

create: create a new game
restart: restart an existing game, host or challenger is allowed to do this
close: close an existing game, which frees up the storage used to store the game, only host is allowed to do this
move: make a movement

玩家将会做出以下动作，与智能合约交互：

* create: 创建游戏
* restart: 重启当前已有的一局游戏，host和challenger都能执行该行为
* close: 关闭一局游戏，将存储资源从EOS中释放，只有host允许操作该行为
* move: 占领某一个位置的格子

#### 创建合约
下面教程，将会基于一个命名为 tic.tac.toe 的账户创建智能合约。如果 tic.tac.toe 账户名被使用，可以创建另外一个账户名，并将代码中出现的 tic.tac.toe 改为你的账户名。
如果还没建立账户，请先按以下命令建立账户，再进行下一步操作。
```
$ cleos create account ${creator_name} ${contract_account_name} ${contract_pub_owner_key} ${contract_pub_active_key} --permission ${creator_name}@active
# e.g. $ cleos create account inita tic.tac.toe  EOS4toFS3YXEQCkuuw1aqDLrtHim86Gz9u3hBdcBw5KNPZcursVHq EOS7d9A3uLe6As66jzN8j44TXJUqJSK3bFjjEEqR4oTvNAB3iM9SA --permission inita@active
```
请确保钱包已经解锁，并且创建者的私钥已经被导入钱包。

### 二、开始
本合约将创建以下三个文件：

* tic_tac_toe.hpp => 定义合约数据结构的头文件
* tic_tac_toe.cpp => 合约主逻辑代码
* tic_tac_toe.abi => 合约ABI文件

### 三、定义数据结构
新建（或打开）`tic_tac_toe.hpp` 文件，加入如下代码模板：
```
// Import necessary library
#include  // Generic eosio library, i.e. print, type, math, etc

using namespace eosio;
namespace tic_tac_toe {
   static const account_name games_account = N(games);
   static const account_name code_account = N(tic.tac.toe); //账户名称，如果需要用其他账户名，在这里改
    // Your code here
}
```
#### 游戏列表

在这个智能合约中，我们需要定义一个Table列表，用来存储所有游戏。
```
...
namespace tic_tac_toe {
    ...
    typedef eosio::multi_index< games_account, game> games;
}
```
其中第一个参数`games_account`定义了table名称。后一个参数`game`定义了游戏结构（Game Structure）。

#### 游戏数据结构 Game Structure
```
...
namespace tic_tac_toe {
   static const uint32_t board_len = 9; //井字游戏3x3，数据长度
   struct game {
      game() {};
      //构造函数
      game(account_name challenger, account_name host):challenger(challenger), host(host), turn(host) {
         // Initialize board
         initialize_board();
      };
      account_name     challenger;
      account_name     host;
      account_name     turn; // = account name of host/ challenger
      account_name     winner = N(none); // = none/ draw/ account name of host/ challenger
      uint8_t          board[9]; //棋盘数组

      // 将3x3上的格子全部清零，即回到最初状态
      void initialize_board() {
         for (uint8_t i = 0; i < board_len ; i++) {
            board[i] = 0;
         }
      }

      // 重启游戏
      void reset_game() {
         initialize_board();
         turn = host;
         winner = N(none);
      }

      auto primary_key() const { return challenger; } //确定table的索引字段为 challenger

      EOSLIB_SERIALIZE( game, (challenger)(host)(turn)(winner)(board) )
   };
```

#### 行为定义
**create** 创建游戏
```
   ...
   struct create {
      account_name   challenger;
      account_name   host;

      EOSLIB_SERIALIZE( create, (challenger)(host) )
   };
   ...
```
**restart** 重启游戏
```
   ...
   struct restart {
      account_name   challenger;
      account_name   host;
      account_name   by; //由谁来发起重启游戏

      EOSLIB_SERIALIZE( restart, (challenger)(host)(by) )
   };
   ...
```
**close** 关闭游戏
```
   ...
   struct close {
      account_name   challenger;
      account_name   host;

      EOSLIB_SERIALIZE( close, (challenger)(host) )
   };
   ...
```
**move** 占格子
```
...
   struct movement {
      uint32_t    row; //行
      uint32_t    column; //列

      EOSLIB_SERIALIZE( movement, (row)(column) )
   };

   struct Move {
      account_name   challenger;
      account_name   host;
      account_name   by; // the account who wants to make the move
      movement       mvt;

      EOSLIB_SERIALIZE( move, (challenger)(host)(by)(mvt) )
   };
   ...
```
至此，hpp文件定义完毕。以上定义的头文件，可在[这里](https://github.com/EOSIO/eos/blob/master/contracts/tic_tac_toe/tic_tac_toe.hpp)获取

### 四、主逻辑代码
打开`tic_tac_toe.cpp`文件，输入以下模板代码：
```
#include <tic_tac_toe.hpp>
using namespace eosio;
/**
*  The apply() method must have C calling convention so that the blockchain can lookup and
*  call these methods.
*/
extern "C" {

   using namespace tic_tac_toe;
   /// The apply method implements the dispatch of events to this contract
   void apply( uint64_t receiver, uint64_t code, uint64_t action ) {
      // Put your action handler here
   }

} // extern "C"
```

#### 行为响应框架
我们只想让合约相应来自于合约创建人帐号，而且不同的行为需要执行不同的命令，因此，创建`impl`结构：
```
using namespace eosio;
namespace tic_tac_toe {
struct impl {
   ...
   /// The apply method implements the dispatch of events to this contract
   void apply( uint64_t receiver, uint64_t code, uint64_t action ) {

      if (code == code_account) {
         if (action == N(create)) {
         	//注意：eosio::unpack_action_data<T>()用于将收到的动作转换为T
            impl::on(eosio::unpack_action_data<tic_tac_toe::create>());
         } else if (action == N(restart)) {
            impl::on(eosio::unpack_action_data<tic_tac_toe::restart>());
         } else if (action == N(close)) {
            impl::on(eosio::unpack_action_data<tic_tac_toe::close>());
         } else if (action == N(move)) {
            impl::on(eosio::unpack_action_data<tic_tac_toe::move>());
         }
      }
   }

};
}

...
extern "C" {

   using namespace tic_tac_toe;
   // apply 方法用来执行广播到该合约的事件
   void apply( uint64_t receiver, uint64_t code, uint64_t action ) {
      //直接执行impl方法
      impl().apply(receiver, code, action);
   }

} // extern "C"
```
在impl结构中，需要添加如下实现方法，先把框架写入，具体实现方法见下文：
```
...
struct impl {
...
   /**
    * @param create - action to be applied
    */
   void on(const create& c) {
      // Put code for create action here
   }

   /**
    * @brief Apply restart action
    * @param restart - action to be applied
    */
   void on(const restart& r) {
      // Put code for restart action here
   }

   /**
    * @brief Apply close action
    * @param close - action to be applied
    */
   void on(const close& c) {
      // Put code for close action here
   }

   /**
    * @brief Apply move action
    * @param move - action to be applied
    */
   void on(const move& m) {
      // Put code for move action here
   }

   /// The apply method implements the dispatch of events to this contract
   void apply( uint64_t receiver, uint64_t code, uint64_t action ) {
...
```

#### 实现 Create 创建游戏
在创建游戏操作中，我们需要做以下几件事情：

* 确保该动作已经host签名
* 确保host和challenger不是同一个玩家
* 确保没有已经存在的游戏
* 将该新建的游戏保存到db中

```
struct impl {
   ...
   /**
    * @brief Apply create action
    * @param create - action to be applied
    */
   void on(const create& c) {
      require_auth(c.host); //从c.host中获取host用户名，并且验证是否已经签名
      eosio_assert(c.challenger != c.host, "challenger shouldn't be the same as host"); //确保host和challenger不是一个玩家

      //检查是否存在相同的游戏
      games existing_host_games(code_account, c.host);
      auto itr = existing_host_games.find( c.challenger );
      eosio_assert(itr == existing_host_games.end(), "game already exists");

      existing_host_games.emplace(c.host, [&]( auto& g ) {
         //判断是否是相同的游戏，即challenger一致，host一致，当前庄家一致
         g.challenger = c.challenger;
         g.host = c.host;
         g.turn = c.host;
      });
   }
   ...
}
```

#### 实现 Close 结束游戏
结束游戏操作中，我们需要做以下事情：

* 确保该动作已经host签名
* 确保游戏存在
* 从链上数据库中删除游戏

```
struct impl {
   ...
   /**
    * @brief Apply close action
    * @param close - action to be applied
    */
   void on(const close& c) {
      require_auth(c.host); //验证host签名

      //检查游戏是否存在，实现方法在上面create中有了
      games existing_host_games(code_account, c.host);
      auto itr = existing_host_games.find( c.challenger );
      eosio_assert(itr != existing_host_games.end(), "game doesn't exists");

      //删除游戏，释放链上资源
      existing_host_games.erase(itr);
   }
   ...
}
```

#### 实现 Move 占位操作
在Move操作中，我们需要实现：

* 确保该操作已经被host或者challenger签名
* 确保游戏存在
* 确保游戏尚未结束
* 确保该操作是有host或者challenger发出的指令
* 确保当前轮正好由正确的玩家操作
* 确认占位操作是有效的
* 更新棋盘数据
* 变更玩家轮次
* 判断是否有胜者
* 保存游戏数据到链上数据库中

```
struct impl {
   ...
   bool is_valid_movement(const movement& mvt, const game& game_for_movement) {
      // 占位是否有效
   }

   account_name get_winner(const game& current_game) {
      // 是否有胜者
   }
   ...
   /**
    * @brief Apply move action
    * @param move - action to be applied
    */
   void on(const move& m) {
      require_auth(m.by); //判断当前轮的用户是否已经签名

      // 检查游戏是否存在
      games existing_host_games(code_account, m.host);
      auto itr = existing_host_games.find( m.challenger );
      eosio_assert(itr != existing_host_games.end(), "game doesn't exists");

      // 检查游戏是否已经结束
      eosio_assert(itr->winner == N(none), "the game has ended!");
      
      // 检查指令是否由host或者challenger发出
      eosio_assert(m.by == itr->host || m.by == itr->challenger, "this is not your game!");
      
      // 检查发送指令的是不是轮到该轮
      eosio_assert(m.by == itr->turn, "it's not your turn yet!");


      // 检查占位是否有效
      eosio_assert(is_valid_movement(m.mvt, *itr), "not a valid movement!");

      // 修改占位数据，1-host，2-challenger
      const auto cell_value = itr->turn == itr->host ? 1 : 2;
      const auto turn = itr->turn == itr->host ? itr->challenger : itr->host;
      existing_host_games.modify(itr, itr->host, [&]( auto& g ) {
         g.board[m.mvt.row * 3 + m.mvt.column] = cell_value;
         g.turn = turn;

         //检查是否有胜者
         g.winner = get_winner(g);
      });
   }
   ...
}
```
验证占位是否有效实现方法：
1- 检查占位是否有效的方法，是否超出9格长度
2- 格子是否位空，即为0
```
struct impl {
   ...
   /**
    * @brief Check if cell is empty 
    * @param cell - value of the cell (should be either 0, 1, or 2)
    * @return true if cell is empty
    */
   bool is_empty_cell(const uint8_t& cell) {
      return cell == 0;
   }

   /**
    * @brief Check for valid movement
    * @detail Movement is considered valid if it is inside the board and done on empty cell
    * @param movement - the movement made by the player
    * @param game - the game on which the movement is being made
    * @return true if movement is valid
    */
   bool is_valid_movement(const movement& mvt, const game& game_for_movement) {
      uint32_t movement_location = mvt.row * 3 + mvt.column;
      bool is_valid = movement_location < board_len && is_empty_cell(game_for_movement.board[movement_location]);
      return is_valid;
   }
   ...
}
```
确定是否胜利的方法：
根据纵向、横向、斜向是否一致，来判断胜负
```
struct impl {
   ...
   /**
    * @brief Get winner of the game
    * @detail Winner of the game is the first player who made three consecutive aligned movement
    * @param game - the game which we want to determine the winner of
    * @return winner of the game (can be either none/ draw/ account name of host/ account name of challenger)
    */
   account_name get_winner(const game& current_game) {
      if((current_game.board[0] == current_game.board[4] && current_game.board[4] == current_game.board[8]) ||
         (current_game.board[1] == current_game.board[4] && current_game.board[4] == current_game.board[7]) ||
         (current_game.board[2] == current_game.board[4] && current_game.board[4] == current_game.board[6]) ||
         (current_game.board[3] == current_game.board[4] && current_game.board[4] == current_game.board[5])) {
         //  - | - | x    x | - | -    - | - | -    - | x | -
         //  - | x | -    - | x | -    x | x | x    - | x | -
         //  x | - | -    - | - | x    - | - | -    - | x | -
         if (current_game.board[4] == 1) {
            return current_game.host;
         } else if (current_game.board[4] == 2) {
            return current_game.challenger;
         }
      } else if ((current_game.board[0] == current_game.board[1] && current_game.board[1] == current_game.board[2]) ||
                 (current_game.board[0] == current_game.board[3] && current_game.board[3] == current_game.board[6])) {
         //  x | x | x       x | - | -
         //  - | - | -       x | - | -
         //  - | - | -       x | - | -
         if (current_game.board[0] == 1) {
            return current_game.host;
         } else if (current_game.board[0] == 2) {
            return current_game.challenger;
         }
      } else if ((current_game.board[2] == current_game.board[5] && current_game.board[5] == current_game.board[8]) ||
                 (current_game.board[6] == current_game.board[7] && current_game.board[7] == current_game.board[8])) {
         //  - | - | -       - | - | x
         //  - | - | -       - | - | x
         //  x | x | x       - | - | x
         if (current_game.board[8] == 1) {
            return current_game.host;
         } else if (current_game.board[8] == 2) {
            return current_game.challenger;
         }
      } else {
         bool is_board_full = true;
         for (uint8_t i = 0; i < board_len; i++) {
            if (is_empty_cell(current_game.board[i])) {
               is_board_full = false;
               break;
            }
         }
         if (is_board_full) {
            return N(draw);
         }
      }
      return N(none);
   }
   ...
}
```

以上，cpp主逻辑代码部分完成，完整代码点击[查看tic_tack_toe.cpp](https://github.com/EOSIO/eos/blob/master/contracts/tic_tac_toe/tic_tac_toe.cpp)

### 五、创建ABI
ABI文件（Application Binary Interface），目的是为了让合约理解发送过去的二进制指令。
ABI文件模板如下：
```
{
  "types": [],
  "structs": [{
      "name": "...",
      "base": "...",
      "fields": { ... }
  }, ...],
  "actions": [{
      "name": "...",
      "type": "...",
      "ricardian_contract": "..."
  }, ...],
  "tables": [{
      "name": "...",
      "type": "...",
      "index_type": "...",
      "key_names" : [...],
      "key_types" : [...]
  }, ...],
  "clauses: [...]
```
ABI文件定义了：struct, action, table，三种数据类型的映射表。与hpp头文件一一对应。

#### 数据库映射（Table）映射ABI
```
{
  ...
  "structs": [{
      "name": "game",
      "base": "",
      "fields": [
        {"name":"challenger", "type":"account_name"},
        {"name":"host", "type":"account_name"},
        {"name":"turn", "type":"account_name"},
        {"name":"winner", "type":"account_name"},
        {"name":"board", "type":"uint8[]"}
      ]
    },...
  ],
  "tables": [{
        "name": "games",
        "type": "game",
        "index_type": "i64",
        "key_names" : ["challenger"],
        "key_types" : ["account_name"]
      }
  ],
  ...
}
```

#### 行为（Action）映射ABI
```
{
  ...
  "structs": [{
    ...
    },{
      "name": "create",
      "base": "",
      "fields": [
        {"name":"challenger", "type":"account_name"},
        {"name":"host", "type":"account_name"}
      ]
    },{
      "name": "restart",
      "base": "",
      "fields": [
        {"name":"challenger", "type":"account_name"},
        {"name":"host", "type":"account_name"},
        {"name":"by", "type":"account_name"}
      ]
    },{
      "name": "close",
      "base": "",
      "fields": [
        {"name":"challenger", "type":"account_name"},
        {"name":"host", "type":"account_name"}
      ]
    },{
      "name": "movement",
      "base": "",
      "fields": [
        {"name":"row", "type":"uint32"},
        {"name":"column", "type":"uint32"}
      ]
    },{
      "name": "move",
      "base": "",
      "fields": [
        {"name":"challenger", "type":"account_name"},
        {"name":"host", "type":"account_name"},
        {"name":"by", "type":"account_name"},
        {"name":"mvt", "type":"movement"}
      ]
    }
  ],
  "actions": [{
      "name": "create",
      "type": "create",
      "ricardian_contract": "" //每个Action都需要对应一个李嘉图合约
    },{
      "name": "restart",
      "type": "restart",
      "ricardian_contract": ""
    },{
      "name": "close",
      "type": "close",
      "ricardian_contract": ""
    },{
      "name": "move",
      "type": "move",
      "ricardian_contract": ""
    }
  ],
  ...
}
```

### 六、编译与部署
略。。。

### 七、怎么玩

**创建游戏**
```
$ cleos push action tic.tac.toe create '{"challenger":"inita", "host":"initb"}' --permission initb@active
```

**占位**
```
$ cleos push action tic.tac.toe move '{"challenger":"inita", "host":"initb", "by":"initb", "mvt":{"row":0, "column":0} }' --permission initb@active 
$ cleos push action tic.tac.toe move '{"challenger":"inita", "host":"initb", "by":"inita", "mvt":{"row":1, "column":1} }' --permission inita@active
```

**重新启动现有游戏**
```
$ cleos push action tic.tac.toe restart '{"challenger":"inita", "host":"initb", "by":"initb"}' --permission initb@active
```

**关闭游戏**
```
$ cleos push action tic.tac.toe close '{"challenger":"inita", "host":"initb"}' --permission initb@active
```

**查看游戏状态**
```
$ cleos get table tic.tac.toe initb games
```


### 八、其他

* 做成网页，前端通过eosjs调用智能合约执行。
* 在合约中加入代币转移，这个游戏则可以立即变成一个有奖惩的小游戏。

