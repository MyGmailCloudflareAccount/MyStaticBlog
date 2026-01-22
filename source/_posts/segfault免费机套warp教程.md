---
title: segfault免费机套warp教程
tags: []
categories: 默认
date: 2025-08-08 03:22:50
---

segfault 上的 wgcf wireguard 管理程序

首先先登录到机器内，然后新建目录

```shell
mkdir warp && cd warp
```

先下载 wgcf 程序

```shell
wget https://github.com/ViRb3/wgcf/releases/download/v2.2.27/wgcf_2.2.27_linux_amd64
mv wgcf_2.2.27_linux_amd64 wgcf
chmod +x wgcf
```

再下载 sfwg 程序

```shell
wget https://github.com/runoneall/sfwg/releases/download/v1/sfwg
chmod +x sfwg
```

生成 warp 配置

```shell
./wgcf register
./wgcf generate
```

使用 sfwg 启动

```shell
./sfwg --up
```

看到 `EXIT:sfwg` 就算成功了

如果已有配置

```shell
./sfwg --use <配置文件路径> --up
```

关闭连接

```shell
./sfwg --down
```

查看连接

```shell
./sfwg --status
```

具体可看 `./sfwg --help`
