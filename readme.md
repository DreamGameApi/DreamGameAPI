
# 开发文档

## 目录

- [开发前必读](#开发前必读)
    - [简介](#简介)
    - [通用说明](#通用说明)
        - [签名规则](#签名规则)
    - [接口文档](#接口文档)
        - [玩家登录](#玩家登录)
        - [转入转出](#转入转出)
        - [游戏列表](#游戏列表)
        - [进入游戏](#进入游戏)
        - [转账订单](#转账订单)
        - [游戏注单](#游戏注单)
        - [玩家信息](#玩家信息)
        - [转账订单确认](#转账订单确认)
 - [游戏](#游戏)
  - [游戏介绍](#游戏介绍)
- [更新日志](#更新日志)
- [联系我们](#联系我们)


# 开发前必读
## 通用说明


### 签名规则
该方式用于直接采用MD5签名的方式

#### 签名算法

##### 第一步
设所有发送或者接收到的数据为集合M，将集合M内非空参数值的参数按照参数名ASCII码从小到大排序（字典序），使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串stringA。

特别注意以下重要规则：
- 参数名ASCII码从小到大排序（字典序）；
- 参数名区分大小写；
- sign参数不参与签名，平台主动通知传送的sign参数不参与签名
- 接口可能增加字段，验证签名时必须支持增加的扩展字段

##### 第二步
在stringA最后拼接上key得到stringSignTemp字符串，并对stringSignTemp进行MD5运算，再将得到的字符串所有字符转换为小写，得到sign值。

举例：
假设传送的参数如下：
```
agent_id： 2
account： 888888
nickname： 12321321
type： 1
ip： 127
timestamp：1706941836000
```

对参数按照key=value的格式，并按照参数名ASCII字典序排序如下：
```go
stringA="agent_id=2&account=888888&nickname=12321321&type=1&ip=127&timestamp=1706941836000";
```

拼接API密钥：
```go
stringSignTemp=stringA + "&key=api_key" //注：api_key为后台设置的密钥key
```



# 接口文档
## 通用错误接口
### 错误码与错误字符串
| 参数名           | 类型      | 描述                |
|---------------|---------|-------------------|
| error_code    | int     | 错误码               |
| error_msg     | string  | 错误信息              |


### 错误码实际内容
| Error Code | Error Message           | 错误码  | 错误信息              |
|------------|-------------------------|---------|---------------------|
| 200        | Success                 | 200     | 成功                  |
| 400        | Failure                 | 400     | 失败                  |
| 401        | Parameter Type Error    | 401     | 参数类型错误            |
| 402        | Parameter Value Error   | 402     | 参数值错误             |
| 403        | Agent Not Found         | 403     | 代理不存在              |
| 404        | Agent Disabled          | 404     | 代理已禁用              |
| 405        | Signature Error         | 405     | 签名错误               |
| 406        | Account Not Found       | 406     | 账号不存在              |
| 407        | Game Not Enabled        | 407     | 游戏未启用              |
| 408        | Token Not Found or Expired | 408   | Token不存在或已过期     |
| 409        | Player Disabled         | 409     | 玩家已禁用              |
| 410        | No Available Game Domain | 410   | 无可用游戏域名           |
| 500        | Internal Error          | 500     | 内部错误               |


## 玩家登录
### 接口概述
- 功能: 玩家登录
- 请求方式: POST
- 请求地址: /api/player/login
### 请求参数
| 参数名     | 类型   | 是否必须 | 描述                                | 示例值          |
|------------|--------|:--------:|-----------------------------------|-----------------|
| account    | string |    是    | 玩家账号                            | p47heuf32rhwi  |
| agent_id   | int64  |    是    | 运营商ID                            | 1               |
| ip         | string |    否    | 玩家IP                              | 127.0.0.1       |
| nickname   | string |    否    | 玩家昵称                            | Nickname11      |
| timestamp  | int64  |    是    | 发送请求的毫秒时间戳                      | 1706941836000      |
| type       | int    |    是    | 玩家类型 0-正常玩家 1-试玩玩家(必填) | 1               |
| sign       | string |    是    | 签名，详见签名规则                    |                 |


### 响应参数
| 参数名               | 类型      | 描述        |
|-------------------|---------|-----------|
| token             | string  | 玩家token   |

### 响应实例
#### 请求成功
```json
{
    "error_code":200,
    "error_msg":"ok",
    "data":{
        "token":"ddec96d2165e4f3e8a642057db116983"
    }
}
```

#### 请求失败

```json
{
    "error_code":401,
    "error_msg":"参数类型错误",
    "data":{}
}
```

## 转入转出
### 接口概述
- 功能: 转入转出
- 请求方式: POST
- 请求地址: /api/trans/inout
### 请求参数
| 参数名    | 类型    | 是否必须 | 描述                    | 示例值              |
|------------|---------|:--------:|-----------------------|---------------------|
| account    | string  |    是    | 玩家账号                | p47heuf32rhwi      |
| agent_id   | int64   |    是    | 运营商ID               | 1                   |
| amount     | string  |    是    | 转入转出金额            | 127.22              |
| remark     | string  |    否    | 备注                    |                     |
| t_order    | string  |    是    | 三方订单号              | O2024012268732      |
| timestamp  | int64   |    是    | 发送请求的毫秒时间戳          | 1706941836000          |
| type       | int     |    是    | 类型(必填) 1-转出 2-转入 | 1                   |
| sign       | string  |    是    | 签名，详见签名规则        |                     |


### 响应参数
| 参数名            | 类型       | 描述                  |
|----------------|----------|---------------------|
| agent_id       | int64    | 运营商ID               |
| order_number   | string   | 订单号                 |
| t_order_number | string   | 三方订单号               |
| create_time    | int64    | 创建时间                |
| account        | string   | 玩家账号                |
| account_type   | int64    | 玩家类型 0-正常 1-试玩     |
| amount         | string   | 转账金额                |
| before_amount  | string   | 转账前金额               |
| after_amount   | string   | 转账后金额               |
| type           | int      | 类型(必填) 1-转出 2-转入  |
| currency_id    | int      | 币种                  |
| remark         | string   | 备注                  |


### 响应实例
#### 请求成功
```json
{
    "error_code":200,
    "error_msg":"ok",
    "data":{
        "agent_id":"1",
        "order_number":"12342342",
        "t_order_number":"134242342",
        "create_time":"1705903529",
        "account":"1234523",
        "account_type":"1",
        "amount":"1.00",
        "before_amount":"0.00",
        "after_amount":"1.00",
        "type":"2",
        "currency_id":"1",
        "remark":""
    }
}
```

#### 请求失败

```json
{
    "error_code":401,
    "error_msg":"参数类型错误",
    "data":{}
}
```

## 游戏列表
### 接口概述
- 功能: 游戏列表
- 请求方式: POST
- 请求地址: /api/game/list
### 请求参数
| 参数名       | 类型     | 是否必须 | 描述           | 示例值         |
|-------------|----------|:----:|--------------|-------------|
| agent_id    | int64    |  是   | 运营商ID        | 1           |
| page        | int64    |  是   | 页数           | 1           |
| page_size   | int64    |  是   | 每页条数         | 10          |
| timestamp   | int64    |  是   | 发送请求的毫秒时间戳     | 1706941836000  |
| sign        | string   |  是   | 签名，详见签名规则    |             |


### 响应参数
| 参数名        | 类型        | 描述                                      |
|--------------|-------------|-----------------------------------------|
| page         | int64       | 页数                                      |    
| page_size    | int64       | 每页条数                                    |
| total        | int64       | 总条数                                     |
| total_page   | int64       | 总页数                                     |
| list         | array       | 列表数组                                    |
| game_id      | string      | 游戏ID                                    |
| game_name    | string      | 游戏名称                                    |
| sub_title    | string      | 游戏副标题                                   |
| cover        | string      | 游戏封面                                    |
| icon         | string      | 游戏图标                                    |
| status       | int         | 状态 1:正常 2:维护                            |
| tag          | int         | 游戏标签 0-全部 1-新品 2-推荐 3-经典 4-人气 5-活跃 6-最近 |
| category     | int         | 游戏分类 1-热门游戏 2-新品游戏                      |
| game_type    | int         | 游戏类型 1:电游类                              |
| volatile     | int         | 游戏波动 1-低 2-中 3-高                        |
| theme        | int         | 游戏主题 1-神话 2-魔幻 3-现代 4-探索 5-自然 6-战争 7-故事 |


### 响应实例
#### 请求成功
```json
{
    "error_code":200,
    "error_msg":"ok",
    "data":{
        "page":"1",
        "page_size":"10",
        "total":"37",
        "total_page":"4",
        "list":[
          {
            "game_id": 1,
            "game_name": "大富豪",
            "sub_title": "BIG",
            "cover": "aaa.jpg",
            "icon": "bbb.jpg",
            "status": 1,
            "tag": 1,
            "category": 1,
            "game_type": 1,
            "volatile": 1,
            "theme": 1
          }
        ]
    }
}
```

#### 请求失败

```json
{
    "error_code":401,
    "error_msg":"参数类型错误",
    "data":{}
}
```

## 进入游戏
### 接口概述
- 功能: 进入游戏
- 请求方式: POST
- 请求地址: /api/enter/game
### 请求参数
| 参数名       | 类型      | 是否必须 | 描述                | 示例值            |
|-------------|---------|:----:|-------------------|------------------|
| agent_id    | int64   |  是   | 运营商ID             | 1                |
| game_id     | int64   |  是   | 游戏ID              | 1                |
| lang        | string  |  是   | 语系                | en_zh            |
| timestamp   | int64   |  是   | 发送请求的毫秒时间戳          | 1706941836000       |
| token       | string  |  是   | 玩家token(玩家注入接口获取) | p47heuf32rhwiq   |
| sign        | string  |  是   | 签名，详见签名规则         |                  |

### 响应参数
| 参数名      | 类型     | 描述    |
|------------|--------|-------|
| play_url   | string | 游戏链接 |


### 响应实例
#### 请求成功
```json
{
    "error_code":200,
    "error_msg":"ok",
    "data":{
        "play_url":"www.game.com/game.html"
    }
}
```

#### 请求失败

```json
{
    "error_code":401,
    "error_msg":"参数类型错误",
    "data":{}
}
```

## 转账订单
### 接口概述
- 功能: 转账订单
- 请求方式: POST
- 请求地址: /api/transfer/order
### 请求参数
| 参数名        | 类型     | 是否必须 | 描述                     | 示例值         |
|-------------|--------|:----:|------------------------|-------------|
| account     | string |  是   | 玩家账号                   | 1           |
| agent_id    | int64  |  是   | 运营商ID                  | 1           |
| end_time    | int64  |  是   | 结束毫秒时间戳                  | 1           |
| page        | int64  |  是   | 页数                     | 1           |
| page_size   | int64  |  是   | 每页条数                   | 10          |
| start_time  | int64  |  是   | 开始毫秒时间戳                  | 1           |
| timestamp   | int64  |  是   | 发送请求的毫秒时间戳               | 1706941836000  |
| type        | int64  |  是   | 玩家类型 0-正常玩家 1-试玩玩家(必填) | 1           |
| sign        | string |  是   | 签名，详见签名规则              |             |

### 响应参数
| 参数名            | 类型     | 描述             |
|----------------|--------|----------------|
| page           | int64  | 页数             |    
| page_size      | int64  | 每页条数           |
| total          | int64  | 总条数            |
| total_page     | int64  | 总页数            |
| list           | array  | 列表数组           |
| order_number   | string | 订单号            |
| t_order_number | string | 三方订单号          |
| create_time    | int    | 创建时间           |
| account        | string | 玩家账号           |
| account_type   | string | 账号类型0-正常 1-试玩  |
| amount         | string | 转账金额           |
| before_amount  | string | 转账前金额          |
| after_amount   | string | 转账后金额          |
| type           | int    | 账变类型 1-转出 2-转入 |
| currency_id    | int    | 币种ID           |
| remark         | string | 备注             |


### 响应实例
#### 请求成功
```json
{
    "error_code":200,
    "error_msg":"ok",
    "data":{
        "page":"1",
        "page_size":"10",
        "total":"37",
        "total_page":"4",
        "list":[
          {
            "order_number": "O37432423",
            "t_order_number": "O37432423",
            "create_time": 1705903529,
            "account": "P3sda3qs",
            "account_type": 1,
            "amount": "1.00",
            "before_amount": "0.00",
            "after_amount": "1.00",
            "type": 1,
            "currency_id": 1,
            "remark": "备注"
          }
        ]
    }
}
```


## 游戏注单
### 接口概述
- 功能: 游戏注单
- 请求方式: POST
- 请求地址: /api/game/orders
- 备注：单次最少获取1分钟区间内所有订单数据，最大不能超过60分钟，我方每分钟更新注单数据，请勿多次重复拉取
### 请求参数
| 参数名         | 类型       | 是否必须 | 描述           | 示例值           |
|-------------|----------|:----:|--------------|---------------|
| agent_id    | int64    |  是   | 运营商ID        | 1             |
| end_time    | int64    |  是   | 结束毫秒时间戳      | 1706941866000 |
| start_time  | int64    |  是   | 开始毫秒时间戳      | 1706941836000 |
| timestamp   | int64    |  是   | 发送请求的毫秒时间戳   | 1706941836000 |
| sign        | string   |  是   | 签名，详见签名规则    |               |

### 响应参数
| 参数名              | 类型     | 描述               |
|------------------|--------|------------------|
| data             | array  | 列表数组             |
| order_id         | string | 订单ID             |
| round_id         | string | 回合ID             |
| bet_id           | string | 注单ID (订单ID+回合ID), 唯一ID|
| game_id          | string | 游戏ID             |
| bet_time         | int    | 下注时间             |
| bet_amount       | string | 下注金额             |
| payout_amount    | string | 派彩金额             |
| overage          | int    | 输赢金额             |
| status           | int    | 交易状态 1-未完成 2-已完成 |
| currency_id      | int    | 币种ID             |
| is_drop_off      | int    | 是否消除 0-否 1-是     |
| is_buy_free      | int    | 是否购买免费 0-否 1-是   |
| PlayerType       | int    | 玩家类型 0-正常 1-试玩   |


### 响应实例
#### 请求成功
```json
{
    "error_code":200,
    "error_msg":"ok",
    "data":[
          {
            "order_id": "5-1709255839-NAMQ1ANSS",
            "round_id": "O374332423",
            "bet_id" : "5-1709255839-NAMQ1ANSS-O374332423",
            "game_id": "1",
            "bet_time": 1706941836000,
            "bet_amount": "1.00",
            "payout_amount": "0.00",
            "overage": "1.00",
            "status": 1,
            "currency_id": 1,
            "is_drop_off": 1,
            "is_buy_free": 1,
            "PlayerType": 1
          }
     ]
}
```
#### 请求失败

```json
{
    "error_code":401,
    "error_msg":"参数类型错误",
    "data":{}
}
```


## 玩家信息
### 接口概述
- 功能: 玩家信息
- 请求方式: POST
- 请求地址: /api/player/info
### 请求参数
| 参数名           | 类型      | 是否必须 | 描述                | 示例值           |
|---------------|---------|:----:|-------------------|---------------|
| account       | string  |  是   | 玩家账号             | p47heuf32rhwi |
| agent_id      | int64   |  是   | 运营商ID            | 1             |
| timestamp     | int64   |  是   | 发送请求的毫秒时间戳        | 1706941836000    |
| sign          | string  |  是   | 签名，详见签名规则       |               |

### 响应参数
| 参数名           | 类型      | 描述                |
|---------------|---------|-------------------|
| account       | string  | 玩家账号              |
| nickname      | string  | 玩家昵称              |
| balance       | string  | 玩家余额              |
| frozen_amount | string  | 玩家冻结              |
| is_online     | int     | 玩家是否在线            |
| account_type  | int     | 玩家类型0-正常 1-试玩     |
| status        | int     | 状态 1-正常 2-冻结 3-封号 |

### 响应实例
#### 请求成功
```json
{
    "error_code":200,
    "error_msg":"ok",
    "data":{
        "account":"aa3242",
        "nickname":"dg",
        "balance":"100.22",
        "frozen_amount":"16.83",
        "is_online":1,
        "account_type":1,
        "status":1
    }
}
```

#### 请求失败

```json
{
    "error_code":401,
    "error_msg":"参数类型错误",
    "data":{}
}
```

## 转账订单确认
### 接口概述
- 功能: 转账订单确认
- 请求方式: POST
- 请求地址: /api/transfer/check
### 请求参数
| 参数名       | 类型       | 是否必须 | 描述           | 示例值          |
|-----------|----------|:----:|--------------|--------------|
| agent_id  | int64    |  是   | 运营商ID        | 1            |
| t_order   | string   |  是   | 第三方订单号       | O38674837624 |
| timestamp | int64    |  是   | 发送请求的时间戳     | 1626863144   |
| sign      | string   |  是   | 签名，详见签名规则    |              |

### 响应参数
| 参数名           | 类型     | 描述                |
|---------------|--------|-------------------|
| error_code    | int    | 错误码               |
| error_msg     | string | 错误信息              |
| t_order       | string | 第三方订单号            |
| is_exit       | int    | 是否存在 1：是   0：否    |
| status        | int    | 订单状态 1：完成   0：未完成 |
| amount        | string | 转账金额              |
| before_amount | string | 转账前金额             |
| after_amount  | string | 转账后金额             |

### 响应实例
#### 请求成功
```json
{
    "error_code":200,
    "error_msg":"ok",
    "data":{
        "t_order":"O348274783264",
        "is_exit":1,
        "status":1,
        "amount":"16.83",
        "before_amount":"100.22",
        "after_amount":"100.22"
    }
}
```

#### 请求失败

```json
{
    "error_code":401,
    "error_msg":"参数类型错误",
    "data":{}
}
```


# 游戏
## 游戏介绍
| 游戏ID | 游戏名称        | 英文名称                      | 题材   | 简介                      |
|------|-------------|---------------------------|------|-------------------------|
| 2    | 麻将胡了        | Mahjong Ways              | 麻将   | 24小时麻将馆/10万倍            |
| 4    | 财神来了        | God of Wealth             | 财神   | 中路连消百搭有机会赢更多            |
| 5    | 赏金猎人        | Bounty Hunter             | 西部   | 高达X40赢奖倍数               |
| 6    | 招财喵         | Lucky Neko                | 奇幻   | 赢特色招财猫符号，送递增奖金倍数        |
| 7    | 寻宝黄金城       | Treasures of Aztec        | 探险   | 多路连消百搭                  |
| 8    | 神鸟报恩        | Divine Bird Returns       | 越南神话 | 杨桃变金币，高达X16200          |
| 9    | 虎虎生财        | Fortune Tiger             | 奇幻   | 重新旋转和X10奖金倍数！           |
| 10   | 热带雨林        | Rain Forest               | 动物   | 无线卷轴递增奖金倍数              |
| 11   | 亡灵大盗        | Wild Bandito              | 奇幻   | 递增式奖金倍数和中轴的金框符号         |
| 12   | 山精水精        | Mountain God vs Water God | 越南神话 | 多种玩法超高倍数，战斗奖金高高高！       |
| 13   | 淘金者         | Gold Rush                 | 探险   | 收集符号触发多种模式，巨龙获得高额奖励！    |
| 14   | 冰雪大冲关       | The Great Icescape        | 动物   | 破冰行动赢取X50000倍投注奖励       |
| 15   | 金球射手        | Ultimate Striker          | 足球   | 编入多路连消百搭，增加奖金倍数最高X8000倍 |
| 16   | 众神宙斯        | 无                         | 北欧神话 | 无                       |
| 17   | 热血欧洲杯       | 无                         | 足球   | 无                       |
| 18   | 狂欢音乐节       | 无                         | 音乐   | 无                       |
