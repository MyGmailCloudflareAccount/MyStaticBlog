---
title: nodebus v3版本发布！
tags: []
categories: 默认
date: 2025-09-20 08:32:28
---

地址：https://github.com/runoneall/nodebus

此次更新有一个 break changes：在 configm 中，将 ssh 的 port 类型从 int 改到 string 了，方便 net.JoinHostPort，至于为什么，net.JoinHostPort 要求 port 是 string 类型

此次改动最大的是 cli 部分，添加了一个新的 command：shell 用于和 run 做区分，因为这两个在 ssh 服务器里事件名不一样：shell 是 shell，run 是 exec，若仅需远程登录 shell 而不执行命令，应该从 v2 的 `run bash` 更改为 `shell`

在 add 部分，添加了从 cli 中指定值（非交互式添加节点）

```plaintext
添加节点

Usage:
  nodebus add [flags]

Flags:
  -h, --help          help for add
      --host string   指定连接地址
      --name string   指定节点名称
      --pass string   指定登录密码
      --port string   指定连接端口
      --user string   指定登录用户

Global Flags:
  -n, --node strings   指定要管理的节点
      --node-all       指定管理全部节点
```

支持组合式添加，比如从 cli 中指定 port 为 22，user 为 root，则将启动交互式输入请求剩余的字段：name，host，pass

在 list 部分也做了小更改，添加了以 json 字符串输出的功能

```plaintext
列出所有节点

Usage:
  nodebus list [flags]

Flags:
  -h, --help         help for list
  -i, --indent int   设置 json 模式下的缩进
  -j, --json         以 json 模式列出所有节点

Global Flags:
  -n, --node strings   指定要管理的节点
      --node-all       指定管理全部节点
```

不指定 --json 参数则会以 v2 的模式输出

悬而未决的问题：

```plaintext
ssh: handshake failed: ssh: unable to authenticate, attempted methods [none], no supported methods remain
```

当登录部分服务器时，如 serv00，会出现以上错误，我怀疑是 `golang.org/x/crypto/ssh` 本身的问题，有没有大佬对这方便有研究的，欢迎赐教！
