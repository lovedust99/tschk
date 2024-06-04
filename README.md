

<div align="center">

# TokenStatusCheck

自用的一个很简单的小功能，用于通义千问、智谱清言等AI的token存活检测，主要用于有比较多的token的情况。可用来自动化更新token数据库实现剔除失效token。

</div>

## 功能

- ✅ 详细的token状态响应。
- ✅ 支持[LLM Red Team-Free-API](https://github.com/LLM-Red-Team)项目，持续更新。
- ✅ 根据接口响应可对接个人数据库（不内置，需自行调用接口，也可自行修改源代码）。
- ⬜ 个人比较忙，有时间的话把各官方平台的token检测也糊上去。
- ⬜ 有时间准备支持配置数据库环境变量，直接修改失效token。

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
4. 
```
cd data

git clone https://github.com/lovedust99/tokenstatuscheck.git
```

5. 编辑data文件下不同项目的`token.json`文件，每行一个token，不要加标点符号。编辑好保存即可，无需重启容器。

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



## 使用示例

POST /api/LLM_TokenCheck/Check/deep

header 需要设置 Authorization 头部：

```
Authorization: Bearer [自己设定的请求头校验值]
```
无需设置请求体

响应数据：
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


## 注意事项

- 建议每日一次调用或者至少每1小时调用，不要频繁调用。

- 可以对接到uptime-kuma等监控平台。

## 其他引用

LLM Red Team : https://github.com/LLM-Red-Team




