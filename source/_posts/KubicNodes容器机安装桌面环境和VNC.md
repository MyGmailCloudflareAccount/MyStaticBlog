---
title: KubicNodes容器机安装桌面环境和VNC
tags: []
categories: 默认
date: 2025-07-26 09:04:31
---

本教程适用于 Ubuntu 22.04

VNC 服务器参考了这篇文章 https://www.cnblogs.com/milton/p/16730512.html

首先申请一台服务器，找到 `bot-commands` 频道，输入：`/deploy password:123123 os:Ubuntu 22.04`

![](https://s.rmimg.com/original/2X/e/e8fca845a7b02e4161bb0160e2aa28169ce68331.png)

开机后会私聊你登录信息

```plaintext
your deployment password
address(es): <ip>:<port1>, <ip>:<port2>, <ip>:<port3>

ssh info:
ssh root@<ip> -p <port>


password: xxxxxx
```

运行ssh登录命令登录，然后安装桌面和VNC服务器

```shell
apt install xfce4 xfce4-goodies tigervnc-standalone-server fonts-noto-cjk
```

安装过程中会提示选择时区和键盘布局，一般是 `English (US) / English (US)` 和 `Asia / Shanghai`

然后先运行一次 `vncserver` 命令（直接用root运行）会让你输入两遍登陆密码

完成后运行下面的命令启动VNC服务器（直接用root运行）

```shell
#!/bin/bash
vncserver -kill -clean
vncserver -localhost no -depth 24 -rfbport <分配给你的三个端口的其中一个>
```

服务器每次重启后都要运行一遍，可以将以上命令保存到shell文件，如 `vnc.sh` 并将其安装到 `/usr/bin` 目录

![](https://s.rmimg.com/original/2X/7/7a26d3dc02ce9bd32b5a8794e9493e53e9db333b.png)

这样直接运行 `vnc.sh` 即可开启

![](https://s.rmimg.com/original/2X/6/6149343a0c3b4968abf39f42c601778fb8a39c64.png)

不以root运行的话，打开某些应用时会出现 `failed to execute xxx (input/output error)` 在线召唤大佬解决，我没弄明白是怎么回事

附一张完整截图

![](https://s.rmimg.com/optimized/2X/f/fc9134b519ffb01559e4af7a64b8de0301fe93e1_2_690x431.jpeg)

占用不是很高，能用
