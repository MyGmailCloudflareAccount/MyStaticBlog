---
title: "nodebus新功能: cfgcenter"
tags: []
categories: 默认
date: 2025-10-01 01:22:34
---

花了一上午的时间，实现了这个设想 `cfgcenter --help`

```plaintext
集中式的管理节点配置

Usage:
  nodebus cfgcenter [flags]

Flags:
  -h, --help          help for cfgcenter
      --host string   指定 cfgcenter 的监听地址 (default "::")
      --port string   指定 cfgcenter 的监听端口 (default "32768")

Global Flags:
      --auth string        连接到 cfgcenter 的认证字符串 (default "none")
      --cfgcenter string   指定 cfgcenter 服务器
  -n, --node strings       指定要管理的节点
      --node-all           指定管理全部节点
```

这样一来，处理并发任务时，只需要在最开始读取一遍文件就可以了，其余的 nodebus 从该服务器拉取配置

服务端用的是 gin http，服务器启动：

```plaintext
cfgcenter --auth 123456
```

host 默认监听全部地址，port 默认为 32768，auth 默认为 none（这里指定为 123456）

配套客户端连接：

```plaintext
--cfgcenter [::]:32768 --auth 123456 list
```

指定 cfgcenter 服务器为 [::]:32768，指定认证为 123456（none 时可以省略）拉取配置并执行 list 操作
