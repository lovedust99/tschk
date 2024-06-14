
<div align="center">

![1](https://github.com/lovedust99/Source/blob/main/pic/lovedust.png?raw=true)

# TokenStatusCheck

自用的一个很简单的小功能，用于全网热门AI及逆向项目的token存活检测，主要用于有比较多的token的情况。可用来自动化更新token数据库实现剔除失效token。

具体说明请查阅：[TS-Chk文档站](https://tschkdoc.pages.dev)

</div>

## 功能

- ✅ 详细的token状态响应。
- ✅ 支持[LLM Red Team-Free-API](https://github.com/LLM-Red-Team)项目，部分项目支持一键获取token。
- ✅ 支持deepseek等官方APIKey用量监控。
- ✅ 支持OpenAI的refreshtoken/accesstoken操作。
- ✅ 支持对接[One-API](https://github.com/songquanpeng/one-api)。
- ✅ 启用数据库自动化维护功能后，失效token不直接删除，会统一存放在其他位置以供查看。
- ✅ `V1.3.0` 版本起增加了可视化支持，提供友好的网页在线管理。提供控制台基本数据看板，支持查询所有服务状态、支持自动化管理、支持日志审计和网页管理等功能。
- ✅ 项目轻量化，本体不含数据库，配置及token数据统一本地化存储。

TODO
- ⬜ 增加状态总结脚本，总结所有服务的状态并返回，支持企业微信、SMTP推送，可以当做日报使用。
- ⬜ 集成国内大模型厂商数据监控服务（目前已支持openai-accesstoken、deepseek）
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
  - [部署](#部署)
  - [环境变量说明](#环境变量说明)

<!-- /code_chunk_output -->



## 准备

- 部署了LLM-Free-api项目或者你在使用DeepSeek的官方Token。
- docker环境
- 具体说明请查阅：[TS-Chk文档站](https://tschkdoc.pages.dev)

## 部署
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
      - OpenAI__chat2api=http(s)://你的lanqian528/chat2api地址/v1/chat/completions
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
  # 以下为项目自带的前端管理服务
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
|可选|OpenAI__chat2api|举例：`http://1.1.1.1/v1/chat/completions`|如果你部署了lanqian528/chat2api项目，可以配置并用于accesstoken检测||
|可选| TokenStatuCheckUrls__DeepTokenCheckUrl | [LLM地址/token/check]  | deepseek |/api/LLM_TokenCheck/Check/deep|
|可选|TokenStatuCheckUrls__QwenTokenCheckUrl|  [LLM地址/token/check] |   通义千问  |/api/LLM_TokenCheck/Check/qwen|
|可选|TokenStatuCheckUrls__HailuoTokenCheckUrl|[LLM地址/token/check]|海螺|/api/LLM_TokenCheck/Check/hailuo|
|可选|TokenStatuCheckUrls__MitaTokenCheckUrl|[LLM地址/token/check]|秘塔搜索|/api/LLM_TokenCheck/Check/mita|
|可选|TokenStatuCheckUrls__StepTokenCheckUrl|[LLM地址/token/check]|跃问Step|/api/LLM_TokenCheck/Check/step|
|可选|TokenStatuCheckUrls__GlmTokenCheckUrl|[LLM地址/token/check]|智谱Glm|/api/LLM_TokenCheck/Check/glm|
|可选|TokenStatuCheckUrls__MoonshotTokenCheckUrl|[LLM地址/token/check]|月之暗面Moonshot|/api/LLM_TokenCheck/Check/moonshot|
|可选|TokenStatuCheckUrls__SparkTokenCheckUrl|[LLM地址/token/check]|讯飞星火|/api/LLM_TokenCheck/Check/spark|

- `UserAuthorization` 是**必填**的，值随便写，建议复杂一点并保证私密性，主要用来做请求校验。
- 根据自己部署项目情况选择性添加环境变量，如果没有某项目那么建议删掉他的环境变量，在填写的时候注意删掉示例值外的 `[ ]` 。

- 如果要启用自动化维护功能，则需要配置数据库连接字符串环境变量。远程数据库防火墙需要同时放行端口，数据库地址可以使用公网地址也可以使用内网地址或容器网络，这里不介绍，具体请问你的AI助手。

具体说明请查阅：[TS-Chk文档站](https://tschkdoc.pages.dev)
具体说明请查阅：[TS-Chk文档站](https://tschkdoc.pages.dev)
具体说明请查阅：[TS-Chk文档站](https://tschkdoc.pages.dev)


