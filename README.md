
<div align="center">

![1](https://github.com/lovedust99/Source/blob/main/pic/lovedust.png?raw=true)

# TokenStatusCheck

自用的一个很简单的小功能，用于全网热门AI及逆向项目的token存活检测，主要用于有比较多的token的情况。可用来自动化更新token数据库实现剔除失效token，还添加了一些取token的实用小工具。

具体说明请查阅：[TS-Chk文档站](https://tschkdoc.pages.dev)

</div>

## 功能

- ✅ 详细的token状态响应。
- ✅ 支持[LLM Red Team-Free-API](https://github.com/LLM-Red-Team)项目，部分项目支持一键批量获取token，支持deepseek等官方APIKey用量监控。
- ✅ 支持OpenAI的refreshtoken/accesstoken操作。
- ✅ 支持对接[One-API](https://github.com/songquanpeng/one-api)。
- ✅ 启用数据库自动化维护功能后，失效账号自动禁用及失效号池重新生效时自动启用。
- ✅ `V1.3.0` 版本起增加了可视化支持，提供友好的网页在线管理。提供控制台基本数据看板，支持查询所有服务状态、支持自动化管理、支持日志审计和网页管理等功能。
- ✅ 支持配置TelegramBot推送

TODO
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

<!-- /code_chunk_output -->



## 准备

- 部署了LLM-Free-api项目或者你在使用DeepSeek、OpenAI的官方Token。
- docker环境
- 具体说明请查阅：[TS-Chk文档站](https://tschkdoc.pages.dev)

## 部署
1. 创建文件夹 `mkdir tschk && cd tschk`
2. 复制并输入`git clone https://github.com/lovedust99/tschk.git`
3. 默认使用3101和3102两个端口，修改端口号请修改docker-compose.yml文件
4. 若无需修改端口号，则直接启动
```
docker-compose up -d
```
4. 查看是否正常启动
```
docker ps
```
*服务更新：*
```
docker-compose down && docker-compose pull && docker-compose up -d
```
部署完成后请看下面不同功能的使用说明

- 如果要启用自动化维护功能，则需要配置数据库连接字符串环境变量。远程数据库防火墙需要同时放行端口，数据库地址可以使用公网地址也可以使用内网地址或容器网络，这里不介绍，具体请问你的AI助手。

具体说明请查阅：[TS-Chk文档站](https://tschkdoc.pages.dev)
具体说明请查阅：[TS-Chk文档站](https://tschkdoc.pages.dev)
具体说明请查阅：[TS-Chk文档站](https://tschkdoc.pages.dev)


