---
title: alpine部署alist
date: 2025-04-29 10:52:15
tags: []
categories: 默认
---

记一次从 claw cloud run 迁移到 fnl mini 的经历，之前已经重装成 alpine 系统了，所以是 alpine 部署 alist 教程

基本版本可从 app launchpad 找到
![](https://s.rmimg.com/2025-04-29/1745916291-632035-2025-04-29-164259.png)

```shell
apk add screen
mkdir alistrun && cd alistrun
wget https://github.com/AlistGo/alist/releases/download/v3.41.0/alist-linux-musl-amd64.tar.gz
tar -xzf alist-linux-musl-amd64.tar.gz
rm alist-linux-musl-amd64.tar.gz
chmod +x alist
screen -R alist
./alist server
<Control + A> d
cd ..
```

在 ./alist server 时，会出现一次行密码，第一次登录需要
![](https://s.rmimg.com/2025-04-29/1745916389-874980-2025-04-29-163903.png)

因为 alpine 使用 musl，所以需要下载 alist 的 musl 版本，若使用默认 gcc 版本会遇到命令无任何输出的情况

替换 releases/download/v3.41.0 可以安装 alist 其他版本
