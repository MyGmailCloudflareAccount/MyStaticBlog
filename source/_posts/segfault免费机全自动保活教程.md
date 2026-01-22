---
title: segfault免费机全自动保活教程
tags: []
categories: 默认
date: 2025-08-03 00:20:27
---

申请地址：https://www.thc.org/segfault/

因为他们关闭了GUI界面，所以此教程基于SSH

此教程需要另一台服务器做转发，可公网可私网，或者你自己的电脑也行（

此教程可全自动保活：SHELL+VNC

1. 申请服务器

```shell
ssh root@segfault.net
```

密码是 `segfault`

然后等待60秒后按下任意键，即可申请成功

2. 保存服务器

![](https://s.rmimg.com/original/2X/6/691f6633562b4e1155c2ad95ddee1e2687ae7b2a.png)

SSH会输出下次连接的密钥，首先安装密钥

```shell
mkdir ~/.ssh
```

```shell
cat >~/.ssh/id_sf-lsd-segfault-net <<'__EOF__'
<密钥内容>
__EOF__
```

再安装SSH配置

```
cat >>~/.ssh/config <<'__EOF__'
host <服务器名>
    User root
    HostName lsd.segfault.net
    IdentityFile ~/.ssh/id_sf-lsd-segfault-net
    SetEnv SECRET=<你的访问码>
    LocalForward *:<VNC连接端口> 0:5900
__EOF__
```

最后修正权限

```shell
chmod 600 ~/.ssh/config ~/.ssh/id_sf-lsd-segfault-net
```

尝试使用 `ssh <服务器名>` 测试连接是否正常

3. 保活服务器

在刚申请的segfault的服务器上也执行一遍上述步骤，然后终端输入

```shell
screen -R vncserver
```

在新shell中使用 `ssh <服务器名>` 连接（这一步可以保活服务器）

先设置VNC密码

```shell
vncpasswd
```

会让你输两遍VNC密码，遇到 `Would you like to enter a view-only password (y/n)?` 选 `n`

然后开启VNC服务器

```shell
vncserver -kill -clean
vncserver :0
```

使用 `Control + A` `D` 将SSH挂到后台，保活就算全部完成了

4. 完结

现在可以使用 `<服务器IP>:<VNC连接端口>` 连接到桌面环境了，也可以随意退出或重连，因为VNC服务挂载到screen里的SSH连接上，也正因为如此，永远有一个活跃的连接，所以你的服务器不会关机或删除
