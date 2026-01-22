---
title: 将webhook转换为websocket的工具
date: 2025-06-16 11:11:32
tags: []
categories: 默认
---

地址：
https://github.com/FramerOrg/HookToWS

特点：

1. 支持多客户端
2. 无需持久化
3. 无需多端口

docker安装：

```shell
docker pull runoneall/hook-to-ws
docker run -d --name hook-to-ws -p 3000:3000 -e WS_AUTH_TOKEN=your_secure_token_here -e PORT=3000 runoneall/hook-to-ws
```

连接：
首先连接ws：

```plaintext
ws://<your_domain>:<port>/?token=<your_token>&path=<your_path>
```

然后程序会在 /webhook/<your_path> 处监听POST请求

对应的webhook地址为：

```plaintext
http://<your_domain>:<port>/webhook/<your_path>
```

如果有ssl那就是 wss 和 https
