---
title: segfault上的openvpn管理程序
tags: []
categories: 默认
date: 2025-08-16 12:34:48
---

地址：https://github.com/runoneall/sfovpn

```shell
wget https://github.com/runoneall/sfovpn/releases/download/v1/sfovpn
chmod +x sfovpn
```

基本用法

`--use` 选定openvpn配置文件，默认 `openvpn.conf`
`--up` 启动vpn
`-d / --down` 关闭vpn
`-s / --status` vpn状态

额外参数

`-u / --user` 选定用户名（若有）
`-p / --password` 选定密码（若有）

具体可看 `./sfovpn -h`
