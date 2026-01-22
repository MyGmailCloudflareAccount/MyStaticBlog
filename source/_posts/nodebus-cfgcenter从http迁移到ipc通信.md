---
title: nodebus cfgcenter从http迁移到ipc通信
tags: []
categories: 默认
date: 2025-10-01 07:46:30
---

[cfgcenter migrated from http to ipc · runoneall/nodebus@3888a60](https://github.com/runoneall/nodebus/commit/3888a60f9972ac16b85ef494962b1526bab34ccc)

代码量可以说是大幅减小了，我封装了一个适用于 nodebus 的 ipc 服务端和客户端（稍微改改就可以集成到你自己的项目中）

[nodebus/ipc at main · runoneall/nodebus](https://github.com/runoneall/nodebus/tree/main/ipc)

使用示例：

1. 服务端： [nodebus/fns/CfgCenterServer.go at main · runoneall/nodebus](https://github.com/runoneall/nodebus/blob/main/fns/CfgCenterServer.go)

2. 客户端： [nodebus/fns/PersistentPreRun.go at main · runoneall/nodebus](https://github.com/runoneall/nodebus/blob/main/fns/PersistentPreRun.go)

以及一些代码结构调整
