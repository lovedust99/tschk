
<div align="center">

![1](https://github.com/lovedust99/Source/blob/main/pic/lovedust.png?raw=true)

# TokenStatusCheck

自用的一个很简单的小功能，用于通义千问、智谱清言等AI的token存活检测，主要用于有比较多的token的情况。可用来自动化更新token数据库实现剔除失效token。

</div>

## 功能

- ✅ 详细的token状态响应。目前已支持：[LLM Red Team-Free-API](https://github.com/LLM-Red-Team)项目、官方API，持续更新。
- ✅ LLM Red Team-Free-API状态监控支持对接[One-API](https://github.com/songquanpeng/one-api)。
- ✅ 启用数据库自动化维护功能后，失效token不直接删除，会统一存放在其他位置以供查看。
- ✅ 增加可视化支持 `V1.3.0` ，提供友好的网页在线管理。提供控制台基本数据看板，支持查询所有服务状态、支持自动化管理、支持日志审计和网页管理等功能。
- ✅ 项目轻量化，本体不含数据库，配置及token数据统一本地化存储。

TODO
- ⬜ 增加状态总结脚本，总结所有服务的状态并返回，支持企业微信、SMTP推送，可以当做日报使用。
- ⬜ 集成国内大模型厂商数据监控服务（目前已支持deepseek）
- ⬜ 增加NewAPI中转支持
- ⬜ 该项目还未与我其他个人业务实现分离，之后会把分离后的代码挂上来。
- ⬜ 如需支持其他内容请提issue。


## 目录
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [TokenStatusCheck](#tokenstatuscheck)
  - [功能](#功能)
  - [目录](#目录)
  - [准备](#准备)
  - [部署使用](#部署使用)
  - [环境变量说明](#环境变量说明)
  - [💦LLM-Free-api-通用状态检测（后端接口） 使用方法](#llm-free-api-通用状态检测后端接口-使用方法)
      - [Uptime-Kuma监控](#uptime-kuma监控)
      - [应用场景扩展](#应用场景扩展)
  - [💦LLM-Free-api-Token自动化维护 使用方法](#llm-free-api-token自动化维护-使用方法)
        - [⭕重要注意事项！](#重要注意事项)
      - [Uptime-Kuma实现自动化维护](#uptime-kuma实现自动化维护)
  - [💦可视化服务](#可视化服务)
  - [💦官方API状态检测（更新中）](#官方api状态检测更新中)
      - [DeepSeek：提供三种查询方式](#deepseek提供三种查询方式)
        - [一：查看token的详细信息](#一查看token的详细信息)
        - [二：通过配置账号列表来查看多token时的每个token的可用额度](#二通过配置账号列表来查看多token时的每个token的可用额度)
        - [三：通过配置API-Key列表来查看多token时的每个token的可用额度](#三通过配置api-key列表来查看多token时的每个token的可用额度)
        - [当然你也可以使用可视化提供的服务进行查询](#当然你也可以使用可视化提供的服务进行查询)
      - [其他更新中](#其他更新中)
  - [注意事项](#注意事项)
  - [其他引用](#其他引用)

<!-- /code_chunk_output -->



## 准备

- 部署了LLM-Free-api项目或者你在使用DeepSeek的官方Token。
- docker环境

## 部署使用
1. 创建文件夹 `mkdir tschk && cd tschk`
2. 使用`Docker-Compose`部署。创建`docker-coompose.yml`文件，并复制以下内容（具体你要写哪些环境变量请参考下方说明）

```
version: '3.8'

services:
# 后端接口
  tschk:
    image: lovedust99/tschk:latest
    restart: always
    environment:
      - UserAuthorization=[自己设定的请求头校验值]
      - TokenStatuCheckUrls__DeepTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__QwenTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__HailuoTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__MitaTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__StepTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__GlmTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__MoonshotTokenCheckUrl=[LLM地址/token/check] 
      - TokenStatuCheckUrls__SparkTokenCheckUrl=[LLM地址/token/check] 
      - ConnectionStrings__TS_Database=server=localhost;port=3306;database=one-api;userid=用户名;password=密码;
    ports:
      - "3010:80"
    networks: 
      - tschk-network
    volumes: 
      - ./data:/app/wwwroot 
  # 以下为项目自带的前端管理服务，如果只调用接口，则下面内容可以删掉
  tschkweb:
    image: lovedust99/tschkweb:latest
    restart: always
    ports:
      - "3020:2000"
    networks: 
      - tschk-network
networks: 
  tschk-network: 
    driver: bridge

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

*服务更新：*
```
docker-compose down && docker-compose pull && docker-compose up -d
```
部署完成后请看下面不同功能的使用说明


## 环境变量说明
| 类型| 环境变量                  | 值            | 说明    |对应的调用接口|
| -----| ------------------- | ------------- | ----------------------------------- |----------------------------------- |
|必填|UserAuthorization|[自己设定的请求头校验值]|自己设定的请求头校验值||
|可选|ConnectionStrings__TS_Database|server=数据库地址;port=数据库端口;database=数据库名称;userid=用户名;password=密码;|自动化维护需要设置的数据库连接字符串||
|可选| TokenStatuCheckUrls__DeepTokenCheckUrl | [LLM地址/token/check]  | deepseek |/api/LLM_TokenCheck/Check/deep|
|可选|TokenStatuCheckUrls__QwenTokenCheckUrl|  [LLM地址/token/check] |   通义千问  |/api/LLM_TokenCheck/Check/qwen|
|可选|TokenStatuCheckUrls__HailuoTokenCheckUrl|[LLM地址/token/check]|海螺|/api/LLM_TokenCheck/Check/hailuo|
|可选|TokenStatuCheckUrls__MitaTokenCheckUrl|[LLM地址/token/check]|秘塔搜索|/api/LLM_TokenCheck/Check/mita|
|可选|TokenStatuCheckUrls__StepTokenCheckUrl|[LLM地址/token/check]|跃问Step|/api/LLM_TokenCheck/Check/step|
|可选|TokenStatuCheckUrls__GlmTokenCheckUrl|[LLM地址/token/check]|智谱Glm|/api/LLM_TokenCheck/Check/glm|
|可选|TokenStatuCheckUrls__MoonshotTokenCheckUrl|[LLM地址/token/check]|月之暗面Moonshot|/api/LLM_TokenCheck/Check/moonshot|
|可选|TokenStatuCheckUrls__SparkTokenCheckUrl|[LLM地址/token/check]|讯飞星火|/api/LLM_TokenCheck/Check/spark|

- `UserAuthorization` 是**必填**的，值随便写，建议复杂一点并保证私密性，主要用来做请求校验。
- 部署了哪个LLM-Free-api项目就把哪个加到环境变量里，如果没有某项目那么建议删掉他的环境变量，注意删掉 `[ ]`。

- 如果要启用自动化维护功能，则需要配置数据库连接字符串环境变量。远程数据库防火墙需要同时放行端口，数据库地址可以使用公网地址也可以使用内网地址或容器网络，这里不介绍，具体请问你的AI助手。

- 如果只是使用deepseek官方token的可以不用填写除`UserAuthorization`外的任何环境变量。

## 💦LLM-Free-api-通用状态检测（后端接口） 使用方法

**如果你只需要调取接口，请阅读这一节；如果你需要自动化维护或使用可视化功能，那么可以跳过这一节。**

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
    #服务名称
    "ServiceName": "LLM-Deep",

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
你可以根据这些返回内容进行二次定制。下面提供一种最简单的通过uptime-kuma进行监控的示例，当然最合适的是配合数据库支持来使用。

#### Uptime-Kuma监控

使用Uptime-Kuma的 `http关键字` 监控类型，具体配置如下：

![1](https://github.com/lovedust99/Source/blob/main/pic/1.jpg?raw=true)


#### 应用场景扩展
部分用户将LLM分为免费和付费版（当然，你可以定义其他场景），两个版本对应不同的token，为了防止token检测时产生混淆，这里提供一种扩展支持。

以上所有的LLM接口后添加 `/free` 即可调用扩展检测接口，例如：
```
https://yoursite.com/api/LLM_TokenCheck/Check/deep/free
```

接口的使用方式与上述一致。同时，你还需要在 `token.json` 同级目录下新增一个 `token2.json` 文件，用于存放免费token，格式与之前所述一致。

## 💦LLM-Free-api-Token自动化维护 使用方法

##### ⭕重要注意事项！
- 目前已支持原生 `One-API` 。使用前请检查自己的项目是否一致或二开项目是否修改过数据库结构。如需其他中转，请提issue，会火速适配。
- 在渠道中，请使用**批量添加功能**，保证**每个渠道只包含一个token**，以便于维护。没有使用批量添加的，请重新修改。图如下：
- **如果你需要可视化服务，那么确保OneAPI/NewAPI渠道与上述方式一致后可以跳过此小节并进入下一节。如果你不需要自动化，但还想使用可视化服务，同样跳过此小节并进入下一节。**

![渠道](https://github.com/lovedust99/Source/blob/main/pic/qudao.png?raw=true)

进入docker-compose.yml同级目录的data文件夹，编辑不同项目文件夹下的`token.json`文件，每行一个token，不要加标点符号。编辑好保存即可，无需重启容器。

`POST` /api/LLM_TokenCheck/Check/deep/one

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

    #请求结果
    "message": "已检查 4 条 token， 1 条 token 已失效， 1 条token已被禁用。token队列已更新；失效token队列已更新。"
}
```

此时，如果数据库环境变量配置正确，在one-api中的该渠道将被禁用，并且在项目文件夹下的 `token.json` 文件中，失效token将被移除并添加至同级目录下的 `expire_token.json` 中。



#### Uptime-Kuma实现自动化维护

**💛推荐：这种方式使用项目自带的可视化管理，提供较为完备的管理项目。**

访问你在 `docker-compose.yml` 设置的前端地址，例如：http://123.123.123.123:3001。

**这种方法不使用项目自带的可视化管理，而是直接调用接口实现自动化维护，更轻量化，但无法获悉实际状态。**
使用Uptime-Kuma的 `http` 监控类型实现每日自动化维护，具体配置如下：

建议将检测频率设置为 `每天一次` ，主要实现自动化。

![monitor-auto](https://github.com/lovedust99/Source/blob/main/pic/monitor-auto.jpg?raw=true)

同时，你还可以借鉴之前的通用状态监测，采用 `更短的检测频率` ，并对外作为状态页展示：例如：

使用Uptime-Kuma的 `http关键字` 监控类型，具体配置如下：

![1](https://github.com/lovedust99/Source/blob/main/pic/1.jpg?raw=true)

## 💦可视化服务

可视化服务可以用来自动化管理token，也可以用来实现最基础的token状态监测。

确保你在docker-compose中配置了前端部分并正常启动。打开ip:前端服务端口，例如：http://1.1.1.1:3020。

在右上角 `配置请求密钥` 中分别填写你在docker-compose.yml中配置的 `UserAuthorization` 及后端服务的地址。在上方的docekr-compose.yml文件示例中，后端地址为：http://你的IP:3310，当然你也可以配置为域名。

![monitor-auto](https://github.com/lovedust99/Source/blob/main/pic/tschkweb.jpg?raw=true)

⭕请注意：前端如果配置为https域名，请求的后端接口也必须为https域名而不是IP地址。

如果想启用自动化服务，你必须确保在docker-compose.yml中配置了你的数据库连接字符串，否则开启自动化后将只判断你的token.json中的token，但不会对你的中转服务数据库造成变化。

建议自动化监测频率为每天：86400秒。


## 💦官方API状态检测（更新中）

#### DeepSeek：提供三种查询方式

##### 一：查看token的详细信息

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
##### 三：通过配置API-Key列表来查看多token时的每个token的可用额度

进入docker-compose.yml同级目录的data文件夹，编辑 `data/OfficialToken/deep_key.json` 文件，每行一条账号信息，格式为`sk-xxxxxxxxx`，不要加标点符号，末尾不要留空格。编辑好保存即可，无需重启容器。

`GET`  /api/OfficialTokenCheck/Check/deep/keylist

请求头：需要设置 Authorization 头部：

```
Authorization: Bearer [自己设定的请求头校验值（来自于环境变量）]
```
返回内容(数组)：
```
{

    "sk-xxxxxx":"10.00-true",
    "sk-xxxxxx":"10.00-true",
    "sk-xxxxxx":"10.00-true",
    "sk-xxxxxx":"10.00-true",
    "sk-xxxxxx":"10.00-false"
}
```

##### 当然你也可以使用可视化提供的服务进行查询

#### 其他更新中



## 注意事项

- **json文件中每一行后面确保不要有空格！！！**

- 建议每日一次调用或者至少每1小时调用，不要频繁调用。

- 可以对接到uptime-kuma等监控平台。

## 其他引用

LLM Red Team : https://github.com/LLM-Red-Team

One-API：https://github.com/songquanpeng/one-api

DeepSeek开放平台：https://platform.deepseek.com

Uptime-Kuma：https://github.com/louislam/uptime-kuma

