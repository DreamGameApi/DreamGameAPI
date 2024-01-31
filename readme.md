
# 开发文档

## 目录

- [开发前必读](#开发前必读)
    - [简介](#简介)
    - [通用说明](#通用说明)
        - [签名规则](#签名规则)
    - [接口文档](#接口文档)
        - [玩家注入](#玩家注入)
        - [转入转出](#转入转出)
        - [游戏列表](#游戏列表)
        - [进入游戏](#进入游戏)
        - [转账订单](#转账订单)
        - [游戏订单](#游戏订单)
        - [玩家信息](#玩家信息)
- [更新日志](#更新日志)
- [联系我们](#联系我们)

# 开发前必读
## 通用说明

```
### 签名规则
```
该方式用于直接采用MD5签名的方式
```

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
timestamp：1626863144831
```

对参数按照key=value的格式，并按照参数名ASCII字典序排序如下：
```go
stringA="agent_id=2&account=888888&nickname=12321321&type=1&ip=127&timestamp=1626863144831";
```

拼接API密钥：
```go
stringSignTemp=stringA + "&key=api_key" //注：api_key为后台设置的密钥key
```



# 接口文档
## 玩家注入
### 接口概述
- 功能: 玩家注入
- 请求方式: POST
- 请求地址: /api/v1/connectify/playerLogin
### 请求参数
| 参数名       | 类型     | 是否必须 | 描述                     | 示例值           |
|-----------|--------|:----:|------------------------|---------------|
| agent_id  | int64  |  是   | 运营商ID                  | 1             |
| account   | string |  是   | 玩家账号                   | p47heuf32rhwi |
| nickname  | string |  否   | 玩家昵称                   | Nickname11    |
| type      | int    |  是   | 玩家类型 0-正常玩家 1-试玩玩家(必填) | 1             |
| ip        | string |  否   | 玩家IP                   | 127.0.0.1     |
| timestamp | int64  |  是   | 发送请求的时间戳,13位带毫秒        | 1626863144831 |
| sign      | int64  |  是   | 签名，详见签名规则              |               |

### 响应参数
| 参数名               | 类型      | 描述        |
|-------------------|---------|-----------|
| error_code        | int     | 错误码       |
| error_msg         | string  | 错误信息      |
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
- 请求地址: /api/v1/connectify/transInOut
### 请求参数
| 参数名         | 类型       | 是否必须 | 描述               | 示例值            |
|-------------|----------|:----:|------------------|----------------|
| agent_id    | int64    |  是   | 运营商ID            | 1              |
| account     | string   |  是   | 玩家账号             | p47heuf32rhwi  |
| nickname    | string   |  是   | 玩家昵称             | Nickname11     |
| type        | int      |  是   | 类型(必填) 1-转出 2-转入 | 1              |
| amount      | float64  |  是   | 转入转出金额           | 127.22         |
| t_order     | string   |  是   | 三方订单号            | O2024012268732 |
| timestamp   | int64    |  是   | 发送请求的时间戳,13位带毫秒  | 1626863144831  |
| remark      | string   |  否   | 备注               |                |
| sign        | int64    |  是   | 签名，详见签名规则        |                |

### 响应参数
| 参数名            | 类型      | 描述               |
|----------------|---------|------------------|
| error_code     | int     | 错误码              |
| error_msg      | string  | 错误信息             |
| agent_id       | int64   | 运营商ID            |
| order_number   | string  | 订单号              |
| t_order_number | string  | 三方订单号            |
| create_time    | int64   | 创建时间             |
| account        | string  | 玩家账号             |
| account_type   | int64   | 玩家类型0-正常 1-试玩    |
| amount         | float64 | 转账金额             |
| before_amount  | float64 | 转账前金额            |
| after_amount   | float64 | 转账后金额            |
| type           | int     | 类型(必填) 1-转出 2-转入 |
| currency_id    | int     | 币种               |
| remark         | string  | 备注               | 

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
- 请求地址: /api/v1/connectify/gameList
### 请求参数
| 参数名       | 类型     | 是否必须 | 描述                     | 示例值           |
|-----------|--------| :------: |------------------------|---------------|
| agent_id  | int64  |    是    | 运营商ID                  | 1             |
| page      | int64 |    是     | 页数                     | 1             |
| page_size | int64 |    是    | 每页条数                   | 10            |
| timestamp | int64  |    是    | 发送请求的时间戳,13位带毫秒   | 1626863144831 |
| sign      | int64  |    是    | 签名，详见签名规则          |               |

### 响应参数
| 参数名        | 类型        | 描述                                      |
|------------|-----------|-----------------------------------------|
| error_code | int       | 错误码                                     |
| error_msg  | string    | 错误信息                                    |
| page       | int64     | 页数                                      |    
| page_size  | int64     | 每页条数                                    |
| total      | int64     | 总条数                                     |
| total_page | int64     | 总页数                                     |
| list       | array     | 列表数组                                    |
| game_id    | string    | 游戏ID                                    |
| game_name  | string    | 游戏名称                                    |
| sub_title  | string    | 游戏副标题                                   |
| cover      | string    | 游戏封面                                    |
| icon       | string    | 游戏图标                                    |
| status     | int       | 状态 1:正常 2:维护                            |
| tag        | int       | 游戏标签 0-全部 1-新品 2-推荐 3-经典 4-人气 5-活跃 6-最近 |
| category   | int       | 游戏分类 1-热门游戏 2-新品游戏                      |
| game_type  | int       | 游戏类型 1:电游类                              |
| volatile   | int       | 游戏波动 1-低 2-中 3-高                        |
| theme      | int       | 游戏主题 1-神话 2-魔幻 3-现代 4-探索 5-自然 6-战争 7-故事 |

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
- 请求地址: /api/v1/connectify/enterGame
### 请求参数
| 参数名       | 类型      | 是否必须 | 描述                | 示例值                  |
|-----------|---------|:----:|-------------------|----------------------|
| agent_id  | int64   |  是   | 运营商ID             | 1                    |
| token     | string  |  是   | 玩家token(玩家注入接口获取) | p47heuf32rhwiq       |
| game_id   | int64   |  是   | 游戏ID              | 1                    |
| lang      | string  |  是   | 语系                | en_zh                |
| timestamp | int64   |  是   | 发送请求的时间戳,13位带毫秒   | 1626863144831        |
| sign      | int64   |  是   | 签名，详见签名规则         |                      |

### 响应参数
| 参数名            | 类型      | 描述   |
|----------------|---------|------|
| error_code     | int     | 错误码  |
| error_msg      | string  | 错误信息 |
| play_url       | string  | 游戏链接 |

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
- 请求地址: /api/v1/connectify/transferOrder
### 请求参数
| 参数名        | 类型     | 是否必须 | 描述                     | 示例值           |
|------------|--------|:----:|------------------------|---------------|
| agent_id   | int64  |  是   | 运营商ID                  | 1             |
| start_time | int64  |  是   | 开始时间戳                  | 1             |
| end_time   | int64  |  是   | 结束时间戳                  | 1             |
| account    | string |  是   | 玩家账号                   | 1             |
| type       | int64  |  是   | 玩家类型 0-正常玩家 1-试玩玩家(必填) | 1             |
| page       | int64  |  是   | 页数                     | 1             |
| page_size  | int64  |  是   | 每页条数                   | 10            |
| timestamp  | int64  |  是   | 发送请求的时间戳,13位带毫秒        | 1626863144831 |
| sign       | int64  |  是   | 签名，详见签名规则              |               |

### 响应参数
| 参数名            | 类型       | 描述             |
|----------------|----------|----------------|
| error_code     | int      | 错误码            |
| error_msg      | string   | 错误信息           |
| page           | int64    | 页数             |    
| page_size      | int64    | 每页条数           |
| total          | int64    | 总条数            |
| total_page     | int64    | 总页数            |
| list           | array    | 列表数组           |
| order_number   | string   | 订单号            |
| t_order_number | string   | 三方订单号          |
| create_time    | int      | 创建时间           |
| account        | string   | 玩家账号           |
| account_type   | string   | 账号类型0-正常 1-试玩  |
| amount         | float    | 转账金额           |
| before_amount  | float    | 转账前金额          |
| after_amount   | float    | 转账后金额          |
| type           | int      | 账变类型 1-转出 2-转入 |
| currency_id    | int      | 币种ID           |
| remark         | string   | 备注             |

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
            "amount": 1.00,
            "before_amount": 0.00,
            "after_amount": 1.00,
            "type": 1,
            "currency_id": 1,
            "remark": "备注"
          }
        ]
    }
}
```


## 游戏订单
### 接口概述
- 功能: 游戏订单
- 请求方式: POST
- 请求地址: /api/v1/connectify/gameOrder
### 请求参数
| 参数名        | 类型      | 是否必须 | 描述                     | 示例值           |
|------------|---------|:----:|------------------------|---------------|
| agent_id   | int64   |  是   | 运营商ID                  | 1             |
| start_time | int64   |  是   | 开始时间戳                  | 1             |
| end_time   | int64   |  是   | 结束时间戳                  | 1             |
| account    | string  |  是   | 玩家账号                   | 1             |
| order_id   | int64   |  是   | 对局号                    | 1             |
| round_id   | int64   |  是   | 回合号                    | 1             |
| game_id    | int64   |  是   | 游戏ID                   | 1             |
| type       | int64   |  是   | 玩家类型 0-正常玩家 1-试玩玩家(必填) | 1             |
| page       | int64   |  是   | 页数                     | 1             |
| page_size  | int64   |  是   | 每页条数                   | 10            |
| timestamp  | int64   |  是   | 发送请求的时间戳,13位带毫秒        | 1626863144831 |
| sign       | int64   |  是   | 签名，详见签名规则              |               |

### 响应参数
| 参数名                 | 类型      | 描述                 |
|---------------------|---------|--------------------|
| error_code          | int     | 错误码                |
| error_msg           | string  | 错误信息               |
| page                | int64   | 页数                 |    
| page_size           | int64   | 每页条数               |
| total               | int64   | 总条数                |
| total_page          | int64   | 总页数                |
| list                | array   | 列表数组               |
| order_id            | string  | 订单ID               |
| round_id            | string  | 回合ID               |
| game_id             | string  | 游戏ID               |
| bet_time            | int     | 下注时间               |
| bet_amount          | float   | 下注金额               |
| payout_amount       | float   | 派彩金额               |
| overage             | int     | 输赢金额               |
| status              | int     | 交易状态 1-未完成 2-已完成   |
| currency_id         | int     | 币种ID               |
| is_drop_off         | int     | 是否消除 0-否 1-是       |
| is_buy_free         | int     | 是否购买免费 0-否 1-是     |
| PlayerType          | int     | 玩家类型 0-正常 1-试玩     |

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
            "order_id": "O37432423",
            "round_id": "O37432423",
            "game_id": "1",
            "bet_time": 1705903529,
            "bet_amount": 1.00,
            "payout_amount": 0.00,
            "overage": 1.00,
            "status": 1,
            "currency_id": 1,
            "is_drop_off": 1,
            "is_buy_free": 1,
            "PlayerType": 1
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


## 玩家信息
### 接口概述
- 功能: 玩家信息
- 请求方式: POST
- 请求地址: /api/v1/connectify/playerLogin
### 请求参数
| 参数名       | 类型     | 是否必须 | 描述                     | 示例值           |
|-----------|--------|:----:|------------------------|---------------|
| agent_id  | int64  |  是   | 运营商ID                  | 1             |
| account   | string |  是   | 玩家账号                   | p47heuf32rhwi |
| timestamp | int64  |  是   | 发送请求的时间戳,13位带毫秒        | 1626863144831 |
| sign      | int64  |  是   | 签名，详见签名规则              |               |

### 响应参数
| 参数名           | 类型     | 描述                |
|---------------|--------|-------------------|
| error_code    | int    | 错误码               |
| error_msg     | string | 错误信息              |
| account       | string | 玩家账号              |
| nickname      | string | 玩家昵称              |
| balance       | float  | 玩家余额              |
| frozen_amount | float  | 玩家冻结              |
| is_online     | int    | 玩家是否在线            |
| account_type  | int    | 玩家类型0-正常 1-试玩     |
| status        | int    | 状态 1-正常 2-冻结 3-封号 |

### 响应实例
#### 请求成功
```json
{
    "error_code":200,
    "error_msg":"ok",
    "data":{
        "account":"aa3242",
        "nickname":"dg",
        "balance":100.22,
        "frozen_amount":16.83,
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


### 错误码
| 错误代码 | 错误描述         |
|------|--------------|
| 400  | 失败           |
| 401  | 参数类型错误       |
| 402  | 参数值错误        |
| 403  | 代理不存在        |
| 404  | 代理已禁用        |
| 405  | 签名错误         |
| 406  | 账号不存在        |
| 407  | 游戏未启用        |
| 408  | Token不存在或已过期 |
| 409  | 玩家已禁用        |
| 410  | 无可用游戏域名      |
| 500  | 内部错误         |
