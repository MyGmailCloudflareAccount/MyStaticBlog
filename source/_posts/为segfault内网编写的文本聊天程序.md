---
title: 为segfault内网编写的文本聊天程序
tags: []
categories: 默认
date: 2025-08-11 16:35:22
---

注意：此程序只能同一节点内使用！比如在 adm 上安装就只能 adm 用，lsd 上安装只能 lsd 用！

项目地址：https://github.com/runoneall/sfchat

包含两个主要文件

1. 服务器文件 `server.py`

如果想要自建服务器，运行 `python3 server.py`

2. 客户端文件 `client.py`

配套的连接到服务器的工具，运行 `python3 client.py`

然后输入服务器端的 `$SF_HOSTNAME` （可以自己连接自己）即可连接
