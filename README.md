
<div align="center">

# TokenStatusCheck

自用的一个很简单的小功能，用于通义千问、智谱清言等AI的token存活检测，主要用于有比较多的token的情况。可用来自动化更新token数据库实现剔除失效token。

</div>

## 功能

- ✅ 详细的token状态响应。
- ✅ 支持[LLM Red Team-Free-API](https://github.com/LLM-Red-Team)项目，持续更新。
- ✅ 支持查看官方API的用量及状态（更新中）。
- ✅ 根据接口响应可对接个人数据库（不内置，需自行调用接口，也可自行修改源代码）。
- ⬜ 有时间准备支持配置数据库环境变量，直接修改失效token。



<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [TokenStatusCheck](#tokenstatuscheck)
  - [功能](#功能)
  - [准备](#准备)
  - [部署使用](#部署使用)
  - [说明](#说明)
  - [LLM-Token状态检测 使用方法](#llm-token状态检测-使用方法)
  - [官方API状态检测（更新中）](#官方api状态检测更新中)
      - [DeepSeek：提供详略两种查询方式](#deepseek提供详略两种查询方式)
        - [一：查看单个token的详细信息](#一查看单个token的详细信息)
        - [二：通过配置账号列表来查看多token时的每个token的可用额度](#二通过配置账号列表来查看多token时的每个token的可用额度)
      - [其他更新中](#其他更新中)
  - [注意事项](#注意事项)
  - [其他引用](#其他引用)

<!-- /code_chunk_output -->



## 准备

- 部署你的LLM-Free-api项目，这里不做赘述。
- docker环境

## 部署使用
1. 创建文件夹 `mkdir tschk && cd tschk`
2. 使用`Docker-Compose`部署。创建`docker-coompose.yml`文件，并复制以下内容

```
version: '3.8'

services:
  tschk:
    image: lovedust99/tschk:latest
    restart: always
    environment:
      - TokenStatuCheckUrls__DeepTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__QwenTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__HailuoTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__MitaTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__StepTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__GlmTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__MoonshotTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__SparkTokenCheckUrl=[LLM地址/token/check] 
      - UserAuthorization=[自己设定的请求头校验值]
    ports:
      - "3000:80"
    volumes: 
      - ./data:/app/wwwroot 
```
3. 启动
```
docker-compose up -d
```
4. 查看是否正常启动
```
docker ps
```

5. 启动成功后
```
cd data

git clone https://github.com/lovedust99/tokenstatuscheck.git .
```


## 说明
| 环境变量                  | 值            | 说明    |对应的调用接口|
| ------------------- | ------------- | ----------------------------------- |----------------------------------- |
| TokenStatuCheckUrls__DeepTokenCheckUrl | [LLM地址/token/check]  | deepseek |/api/LLM_TokenCheck/Check/deep|
|TokenStatuCheckUrls__QwenTokenCheckUrl|  [LLM地址/token/check] |   通义千问  |/api/LLM_TokenCheck/Check/qwen|
|TokenStatuCheckUrls__HailuoTokenCheckUrl|[LLM地址/token/check]|海螺|/api/LLM_TokenCheck/Check/hailuo|
|TokenStatuCheckUrls__MitaTokenCheckUrl|[LLM地址/token/check]|秘塔搜索|/api/LLM_TokenCheck/Check/mita|
|TokenStatuCheckUrls__StepTokenCheckUrl|[LLM地址/token/check]|跃问Step|/api/LLM_TokenCheck/Check/step|
|TokenStatuCheckUrls__GlmTokenCheckUrl|[LLM地址/token/check]|智谱Glm|/api/LLM_TokenCheck/Check/glm|
|TokenStatuCheckUrls__MoonshotTokenCheckUrl|[LLM地址/token/check]|月之暗面Moonshot|/api/LLM_TokenCheck/Check/moonshot|
|TokenStatuCheckUrls__SparkTokenCheckUrl|[LLM地址/token/check]|讯飞星火|/api/LLM_TokenCheck/Check/spark|
|UserAuthorization|[自己设定的请求头校验值]|自己设定的请求头校验值||

部署了哪个就把哪个加到环境变量里，`UserAuthorization`是**必填**的，值随便写，主要用来做请求校验。



## LLM-Token状态检测 使用方法

进入docker-compose.yml同级目录的data文件夹，编辑不同项目文件夹下的`token.json`文件，每行一个token，不要加标点符号。编辑好保存即可，无需重启容器。

`POST` /api/LLM_TokenCheck/Check/deep

请求头：需要设置 Authorization 头部：

```
Authorization: Bearer [自己设定的请求头校验值]
```
无需设置请求体

响应数据示例：
```
{
    #token队列整体的状态，50%以上的token有效时为tokentrue，低于50%为tokenfalse
    "tokenStatus": "tokentrue",

    #token队列中token总数
    "totalTokenCount": 4,

    #token队列中失效的token总数
    "invalidCount": 1,

    #token队列中失效token在队列中的位置，数组中有几代表第几个
    "invalidTokenPositions": [2],
    
    #token队列中可用token占所有token的比例
    "validTokenPercentage": "75%"
}
```
你可以根据这些返回内容进行二次定制。

## 官方API状态检测（更新中）

#### DeepSeek：提供详略两种查询方式

##### 一：查看单个token的详细信息

首次从官方API平台创建token时（也可以随时登录获取），打开用量信息页面：https://platform.deepseek.com/usage ，然后F12打开开发者工具，从Application > LocalStorage本地存储中找到userToken中的value值，这将作为`Authorization`的`Bearer Token`值：`Authorization: Bearer TOKEN`

这个token可以保存下来，以便日后调用。

`GET`  /api/OfficialTokenCheck/Check/deep/single

请求头：需要设置 Authorization 头部：

```
Authorization: Bearer TOKEN
```
返回内容：
```
{
    "code": 0,
    "msg": "",
    "data": {
        "current_token": 10000000,
        "monthly_usage": 0,
        "total_usage": 0,
        "normal_wallets": [
            {
                "currency": "CNY",

                #充值余额
                "balance": "0E-20", 

                #充值余额可用的Token数量
                "token_estimation": "0" 
            }
        ],
        "bonus_wallets": [
            {
                "currency": "CNY",

                #赠送余额
                "balance": "10.00000000000000000000", 

                #赠送余额可用的Token数量
                "token_estimation": "5000000" 
            }
        ],
        #充值余额+赠送余额可用的Token总数量
        "total_available_token_estimation": "5000000", 
        "monthly_costs": [
            {
                "currency": "CNY",
                #月度消费金额
                "amount": "0"  
            }
        ],
        #月度Token使用量
        "monthly_token_usage": 0 
    }
}
```
##### 二：通过配置账号列表来查看多token时的每个token的可用额度

进入docker-compose.yml同级目录的data文件夹，编辑 `data/OfficialToken/deep_user.json` 文件，每行一条账号信息，格式为`13912341234-password`（中间通过 `-` 连接），不要加标点符号，末尾不要留空格。编辑好保存即可，无需重启容器。

`GET`  /api/OfficialTokenCheck/Check/deep/list

请求头：需要设置 Authorization 头部：

```
Authorization: Bearer [自己设定的请求头校验值（来自于环境变量）]
```
返回内容(数组)：
```
[
  # 与json文件中的账号顺序一致，这里只显示可用总额度，包括充值额度和赠送额度
    "5000000",
    "4722170",
    "4742114",
    "4705261",
    "4659117"
]
```


#### 其他更新中
目前已支持DeepSeek，无需实名认证即可使用官方Token，其他平台均需实名认证，批量获取状态意义不大。


## 注意事项

- **json文件中每一行后面确保不要有空格！！！**

- 建议每日一次调用或者至少每1小时调用，不要频繁调用。

- 可以对接到uptime-kuma等监控平台。

## 其他引用

LLM Red Team : https://github.com/LLM-Red-Team
DeepSeek开放平台：https://platform.deepseek.com/
